<template>
    <div id="orderDetail" v-show="isSwitchOrder">
        <div class="detailHeader" @click="returnBackOrder">
            <i class="el-icon-back" ></i>
        </div>
        <div class="detailMain">
            <div class="detailLines"  >
                <div class="detailLine detailLine1"><span>订单号 : {{details.number}}</span><span>订单类型 : {{details.orderType == 1?'新增服务订单':details.orderType == 2?'新增上门订单':details.orderType == 3?'追单':details.orderType == 4?'商城订单':'暂无订单类型'}}</span><span>小区名 : {{details.commodityName}}</span></div>
                <div class="detailLine detailLine2"><span>下单时间 : {{details.createTime}}</span><span>支付状态 : {{details.payState == 1?'已支付':details.payState == 2?'未支付':details.payState == 3?'已过期':details.payState == 4?'pos机':'暂无支付状态'}}</span><span>支付金额 : {{details.paidMoney}}</span></div>
                <div class="detailLine detailLine4"><span>待付金额 : {{details.unpaidMoney}}</span><span>订单状态 : {{details.orderState == 1?'未完成':details.orderState == 2?'已完成':details.orderState == 3?'异常订单':details.orderState == 4?'退款中':details.orderState == 5?'退款完成':details.orderState == 6?'取消订单':details.orderState == 7?'退款驳回':'暂无订单状态'}}</span></div>
                <div class="lineAddress">详细地址 : {{details.detailAddress}}</div>
                <div class="lineOriginDetail"><span>来源详细 : </span><el-input type="textarea" :rows="2" placeholder="请输入内容" >{{details.sourceDetail}}</el-input></div>
                <div class="lineRepairRecord"><span>维修故障记录 : </span><el-input type="textarea" :rows="2" placeholder="请输入内容">{{details.remark}}</el-input></div>
            </div>
        </div>
    </div>
</template>
<script>
export default{
    data () {
        return {
            details:{},
            isSwitchOrder:false,
        }
    },
    created:function(){
        this.$root.$on('orderShow',(data) => {
            this.isSwitchOrder=true;
            this.details = data
        })
    },
    methods: {
        returnBackOrder(){
            this.isSwitchOrder = false
            this.$root.$emit('detailShow')
        },
    },
    beforeDestroy(){
        this.$root.$off('orderShow')
    }
}
</script>
<style scoped lang="less">
#orderDetail{
    min-width: 750px;
    padding:20px 20px;
    background: #fff;
    .detailHeader{
        width: 28px;
        height: 28px;
        line-height: 30px;
        text-align: center;
        border-radius:50%;
        border:2px solid #409EFF;
        cursor: pointer;
        i{
            color:#409EFF;
            font-size:20px;
            font-weight:900;
        }
    }
    .detailMain{
        background:#f8f8f8;
        margin-top:30px;
        padding:30px 60px 30px 80px;
        .detailLines{
            .detailLine{
                display:flex;
                justify-content: space-between;
                align-items: center;
                margin-bottom:50px;
                font-size: 14px;
                color:#102a42;
                span{
                    display: block;
                    width: 200px;
                    text-align: left;
                }
            }
            .lineAddress{
                font-size: 14px;
                color:#102a42;
                margin-bottom:50px;
            }
            .lineOriginDetail,.lineRepairRecord{
                margin-bottom:50px;
                font-size: 14px;
                color:#102a42;
                display:flex;
                span{
                    display: block;
                    width: 80px;
                    line-height: 25px;  
                }     
            }
            .lineRepairRecord{
                margin-left:-30px;
                span{
                    width: 120px;   
                }
            }
        }
    }
}
</style>
<style>
.detailLines .el-textarea__inner{
    resize: none !important;
}
</style>
