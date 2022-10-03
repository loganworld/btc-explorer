<template>
    <div class="wallet-list-dt">
        <template v-if="!dBlockListError">
            <f-data-table
                :columns="dColumns"
                :items="accounts"
                :disable-infinite-scroll="!dHasNext"
                :loading="cLoading"
                infinite-scroll
                fixed-header
                v-bind="{ ...$attrs, ...$props }"
                class="f-data-table-body-bg-color"
                @fetch-more="fetchMore"
            >
                <template v-slot:column-address="{ value }">
                    <div class="row no-collapse no-vert-col-padding">
                        <div class="col">
                            <router-link
                                :to="{
                                    name: 'address-detail',
                                    params: { id: value }
                                }"
                                :title="value"
                                >{{ value }}</router-link
                            >
                        </div>
                    </div>
                </template>
            </f-data-table>
        </template>

        <template v-else>
            <div class="query-error">{{ dBlockListError }}</div>
        </template>
    </div>
</template>

<script>
// import config from "../../app.config.js";
// import FDataTable from "../components/FDataTable.vue";
import FDataTable from "../components/core/FDataTable/FDataTable.vue";
// import gql from "graphql-tag";
import { WEITo } from "../utils/transactions.js";
import { timestampToDate } from "../filters.js";
// import { cloneObject } from "@/utils";
import axios from "axios";

export default {
    components: {
        FDataTable
    },

    props: {
        hiddenColumns: {
            ...FDataTable.props.hiddenColumns
        },
        /** Number of items per page. */
        itemsPerPage: {
            type: Number,
            default: 40
        }
    },

    // apollo: {
    //     blocks: {
    //         query: gql`
    //             query BlockList($cursor: Cursor, $count: Int!) {
    //                 blocks(cursor: $cursor, count: $count) {
    //                     totalCount
    //                     pageInfo {
    //                         first
    //                         last
    //                         hasNext
    //                         hasPrevious
    //                     }
    //                     edges {
    //                         block {
    //                             hash
    //                             number
    //                             timestamp
    //                             transactionCount
    //                             gasUsed
    //                         }
    //                         cursor
    //                     }
    //                 }
    //             }
    //         `,
    //         variables() {
    //             return {
    //                 cursor: null,
    //                 count: this.itemsPerPage
    //             };
    //         },
    //         result(_data, _key) {
    //             let data;

    //             if (_key === "blocks") {
    //                 // console.log('???');
    //                 data = _data.data.blocks;

    //                 const edges = cloneObject(data.edges);

    //                 this.dHasNext = data.pageInfo.hasNext;

    //                 if (this.dItems.length === 0) {
    //                     this.dItems = edges;
    //                 } else {
    //                     for (let i = 0, len1 = edges.length; i < len1; i++) {
    //                         this.dItems.push(edges[i]);
    //                     }
    //                 }

    //                 this.$emit(
    //                     "records-count",
    //                     formatHexToInt(data.totalCount)
    //                 );
    //             }
    //         },
    //         error(_error) {
    //             this.dBlockListError = _error.message;
    //         }
    //     }
    // },

    data() {
        return {
            dItems: [],
            isLoading: true,
            dHasNext: false,
            dBlockListError: "",
            gasPrice: this.$store.state.gasPrice,
            count: 0,
            accounts: [],
            dColumns: [
                {
                    name: "rank",
                    label: "Rank",
                    itemProp: "rank",
                    width: "60px"
                },
                {
                    name: "address",
                    label: "Address",
                    itemProp: "_id",
                    width: "340px"
                },
                {
                    name: "balance",
                    label: "Balance",
                    itemProp: "balance"
                },
                {
                    name: "percent",
                    label: "Percentage",
                    itemProp: "balance",
                    formatter: _value =>
                        ((_value * 100) / 22000000)
                            .toFixed(8)
                            .replace(/(\.0+|0+)$/, "") + "%"
                    // width: '80px'
                }
            ]
        };
    },

    computed: {
        cLoading() {
            return this.isLoading; // this.$apollo.queries.blocks.loading;
        }
    },

    mounted() {
        this.fetchData();
    },
    methods: {
        async fetchData() {
            try {
                let res = await axios.post(
                    "https://api.bitcoinevm.co/api/top-accounts"
                );
                this.isLoading = false;
                this.accounts = res.data.accounts.map((account, index) =>
                    Object.assign(account, { rank: index + 1 })
                );
                this.$emit("records-count", res.data.count);
            } catch (err) {
                console.log(err);
            }
        },
        fetchMore() {
            const { blocks } = this;

            if (blocks && blocks.pageInfo && blocks.pageInfo.hasNext) {
                const cursor = blocks.pageInfo.last;

                this.$apollo.queries.blocks.fetchMore({
                    variables: {
                        cursor,
                        count: this.itemsPerPage
                    },
                    updateQuery: (previousResult, { fetchMoreResult }) => {
                        // this.dHasNext = fetchMoreResult.blocks.pageInfo.hasNext;

                        return fetchMoreResult;
                        /*
                            return {
                                blocks: {
                                    ...fetchMoreResult.blocks,
                                    edges: [...previousResult.blocks.edges, ...fetchMoreResult.blocks.edges]
                                }
                            }
*/
                    }
                });
            }
        },

        WEITo,
        timestampToDate
    }
};
</script>
