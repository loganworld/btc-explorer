<template>
    <div class="f-crypto-symbol">
        <span class="img" :style="{ width: imgWidth, height: imgHeight }">
          <icon v-if="svgIcon" :data="svgIcon" original />
            <!-- <img
                v-else-if="token.logoUrl || token.logoURL"
                :src="token.logoUrl || token.logoURL"
                class="not-fluid"
                :alt="$defi.getTokenSymbol(token)"
            /> -->
        </span>
        <router-link v-if="!noSymbol" :to="{name: 'asset-detail', params: {address: token.address}}" :title="token.address">{{ $defi.getTokenSymbol(token) }}</router-link>  
    </div>
</template>

<script>
import config from "../../../../app.config";
import Icon from "../../../assets/svg/tokens/logo.svg";
import wIcon from "../../../assets/svg/tokens/logo.svg";
import usdIcon from "../../../assets/svg/tokens/USD.svg";

/**
 * Render crypto logo and name by given token.
 * Requires vue defi plugin ($defi).
 */
export default {
    name: "FCryptoSymbol",

    props: {
        /** @type {DefiToken} */
        token: {
            type: Object,
            default() {
                return null;
            },
            required: true
        },
        imgWidth: {
            type: String,
            default: "40px"
        },
        imgHeight: {
            type: String,
            default: "40px"
        },
        noSymbol: {
            type: Boolean,
            default: false
        }
    },

    data() {
        return {
            logoUrl: this.token ? this.token.logoUrl || this.token.logoURL : ""
        };
    },

    computed: {
        svgIcon() {
            const { token } = this;
            if (token) {
                switch (token.symbol) {
                    case config.symbol:
                        return Icon;
                    case config.wsymbol:
                        return wIcon;
                    case config.usdsymbol:
                        return usdIcon;
                }
            }

            return Icon;
        }
    }
};
</script>

<style lang="scss">
@import "style";
</style>
