<template>
    <div class="narrow-container">
        <f-view-heading>
            <h1>
                {{ $t("view_wallet_list.title") }}
                <span v-if="dRecordsCount" class="f-records-count"
                    >({{ dRecordsCount }})</span
                >
            </h1>
        </f-view-heading>
        <f-wallet-list @records-count="onRecordsCount"></f-wallet-list>
    </div>
</template>

<script>
import FViewHeading from "../components/FViewHeading.vue";
import FWalletList from "../data-tables/FWalletList.vue";

export default {
    components: {
        FViewHeading,
        FWalletList
    },

    data() {
        return {
            dRecordsCount: 0
        };
    },

    methods: {
        onRecordsCount(_num) {
            this.dRecordsCount = _num;
        }
    }
};
</script>

<style lang="scss"></style>
