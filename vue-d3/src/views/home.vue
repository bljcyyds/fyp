<template>
    <div style="height:100%;">
        <div class="header">
            <div class="selectRange">
                <Menu mode="horizontal" @on-select="(name) =>$router.push(name)" :active-name="$route.name">
                    <MenuItem name="page3">
                        HomePage
                    </MenuItem>
                </Menu>
            </div>
            <div class="header-title">
                Yelp Dataset Visualization
            </div>
            <div class="selectRange">
           
            </div>
        </div>
   
        <div class="page">
            <router-view v-if="flag" :selectRangeDate='selectRangeDate'></router-view>
        </div>

    </div>
</template>

<script>
export default {
    name: '',
    data() {
        return {
            activeName: 'month',// 默认显示近一月
            modal: false,
            flag: false,
            selectRangeDate: [],
            startTime: '',
            endTime: '',
            optionStart: {
                disabledDate(date) { // 禁止选择今天之后的日期
                    return date && date.valueOf() > Date.now() - 86400000;
                }
            },
            optionEnd: {},
            resizeFn: null
        }
    },
    mounted() {
        window.addEventListener('resize', this.resizeFn);
        this.handleSelect(this.activeName); // 默认显示近一个月
    },
    methods: {

        getMonthBetween(start, end) {  // 获取开始时间和结束时间之内的所有月份
            this.selectRangeDate = [];
            let s = start.split("-");  // 字符串装换数组
            let e = end.split("-");
            let date = new Date();
            let min = date.setFullYear(s[0], s[1] - 1); // 设置最小时间
            let max = date.setFullYear(e[0], e[1] - 1); // 设置最大时间
            let curr = min;
            while (curr <= max) {  // 循环添加月份
                var month = curr.getMonth();
                var arr = [curr.getFullYear(), month + 1];
                this.selectRangeDate.push(arr);
                curr.setMonth(month + 1);
            }
        },
        getDays(day) {// 获取天数
            let arr = [];
            for (let i = -day; i < 0; i++) { // 循环添加天数
                let today = new Date();// 获取今天
                let targetday_milliseconds = today.getTime() + 1000 * 60 * 60 * 24 * i;
                today.setTime(targetday_milliseconds); //设置i天前的时间
                let tYear = today.getFullYear();
                let tMonth = today.getMonth();
                let tDate = today.getDate();
                let date = [tYear, tMonth + 1, tDate];
                arr.push(date);
            }
            return arr
        },
        handleSelect(name) {
            switch (name) {
                case 'day':

                    break;
                case 'week':

                    this.selectRangeDate = this.getDays(7);// 获取近一周的天数
                    this.flag = true;

                    break;
                case 'month':
                    this.selectRangeDate = this.getDays(30);// 获取近一个月的天数
                    this.flag = true;
                    break;
                case 'filter':
                    this.modal = true;
                    break;
                default:
                    break;
            }

        }
    },
}
</script>

<style lang="less">


.header {
    height: 80px;
    background: rgba(178,210,221);
    display: flex;
    justify-content: space-between;
    align-items: center;

    &-title {
        color: #000000;
        font-size: 30px;
        letter-spacing: 5px;
    }

    .selectRange {
        height: 100%;

        .ivu-menu-horizontal {
            background: rgba(54, 136, 190, 0);

            &::after {
                height: 0;
            }

            .ivu-menu-item-active {
                border-bottom: 2px solid #264e5e;

            }

            .ivu-menu-item, .ivu-menu-submenu {
                color: #000000;

                &:hover {
                    border-bottom: 2px solid #264e5e;
                }
            }

            .ivu-select-dropdown {
                background: #09102E;

                .ivu-menu-item {
                    // color: #75deef;

                    &:hover {

                        // border-bottom: 2px solid #264e5e;
                        background-color: rgba(255, 255, 255, 0);
                    }
                }
            }

        }
    }
}

.page {
    height: calc(~ '100% - 80px');

}
</style>
