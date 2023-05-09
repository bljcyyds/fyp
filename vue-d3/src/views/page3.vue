<template>
    <Row class="page3">
        <Col style="width:26%;">
        <div class="left-1">

            <distributionRating ref="distributionRating" v-on:click-line="updateRectColor" v-on:click-line1="updateMapState"
                v-on:unclick-line="updateRectColor1" v-on:unclick-line1="updateMapState1"></distributionRating>
        </div>

        <div class="left-2">
            <wordCloud ref="wordCloud">
            </wordCloud>
        </div>

        </Col>
        <Col style="width:48%;padding:0 1%;">
        <div class="center-top">
            <yelp-map ref="yelpMap"></yelp-map>
        </div>
        <div class="center-bottom">
            <network ref="network" v-on:click-network="clickNetwork" v-on:click-network1="clickNetwork1" v-on:click-network2="clickNetwork2" v-on:click-network3="clickNetwork3"></network>
        </div>
        </Col>
        <Col style="width:26%">
        <div class="right-1">
            <restaurantRect ref='restaurantRect' v-on:click-rect="updateMap" v-on:unclick-rect="updateMap"></restaurantRect>
        </div>
        <div class="right-2">
            
            <radar ref="radar"></radar>
            
        </div>
        </Col>
    </Row>
</template>

<script>
const yelpMap = () => import('./components/page3/yelpMap');
const distributionRating = () => import('./components/page3/distributionRating');
const wordCloud = () => import('./components/page3/wordCloud');
const network = () => import('./components/network');
const restaurantRect = () => import('./components/page3/restaurantRect');
const radar = () => import('./components/page3/radar');

export default {
    name: 'page3',
    components: {
        yelpMap,
        distributionRating,
        network,
        restaurantRect,
        wordCloud,
        radar,
    },
    methods: {
        updateMap(name, sentiment_score) {
            this.$refs.yelpMap.updateMap(name, sentiment_score);
        },
        updateRectColor(state) {
            this.$refs.restaurantRect.updateRectColor(state);
        },
        updateMapState(state) {
            this.$refs.yelpMap.updateMapState(state);
        },
        updateMap1(name, sentiment_score) {
            this.$refs.yelpMap.updateMap1(name, sentiment_score);
        },
        updateRectColor1(state) {
            this.$refs.restaurantRect.updateRectColor1(state);
        },
        updateMapState1(state) {
            this.$refs.yelpMap.updateMapState1(state);
        },
        clickNetwork(name) {
            this.$refs.wordCloud.clickNetwork(name);
        },
        clickNetwork1(name) {
            this.$refs.wordCloud.clickNetwork1(name);
        },
        clickNetwork2(name) {
            this.$refs.radar.updateRadar(name);
        },
        clickNetwork3() {
            this.$refs.radar.drawChart();
        },
    },
    mounted() {
        this.resizeFn = this.$debounce(() => {
        }, 500)
        window.addEventListener('resize', this.resizeFn)
    },
    beforeDestroy() {
        window.removeEventListener('resize', this.resizeFn)
    }
}
</script>

<style lang="less" scoped>
.page3 {
    height: 100%;
    width: 100%;
    padding: 14px 20px 20px;
    background: rgba(178, 210, 221);
    overflow: hidden;

    .ivu-col {
        height: 100%;
        float: left;
    }

    .left-1 {
        height: 50%;
    }

    .left-2 {
        height: 50%;
    }



    .center-top {
        height: 50%;
    }

    .center-bottom {
        height: 50%;
    }

    .right-1 {
        height: 50%;

    }

    .right-2 {
        height: 50%;

    }
}
</style>
