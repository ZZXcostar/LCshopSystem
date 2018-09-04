<template>
    <div id="order">
        <div class="isSwitch" v-show="isSwitchOrderDetail">
            <ul class="orderMain">
                <li v-for="info in infos" :key="info.id" >
                    <div class="orderDiv"><span>订单号 : {{info.number}}</span><span>支付金额 : {{info.paidMoney}}</span><span>订单状态 : 
                        {{info.orderState == 1?"未完成":info.orderState == 2?"已完成":info.orderState == 3?"异常订单":info.orderState == 4?"退款中":
                        info.orderState == 5?"退款完成":info.orderState == 6?"取消订单":info.orderState == 7?"退款驳回":''}}</span></div>
                    <div class="orderDiv"><span>下单时间 : {{info.createTime}}</span></div>
                    <div class="orderBtn">
                        <div class="btnGroup">
                            <el-button @click="jumpOrderDetail(info)" type="primary" style="border-radius:4px;">查看详情</el-button>
                            <el-button type="primary" style="border-radius:4px;">回访</el-button>
                            <el-button type="primary" style="border-radius:4px;">追单</el-button>
                            <el-button type="primary" style="border-radius:4px;">退款</el-button>
                        </div>
                    </div>
                </li>
            </ul>
            
            <!-- <public-pagination></public-pagination> -->
        </div>
        <order-details></order-details>
    </div>
</template>
<script>
import publicPagination from '@/components/common/pagination/pagination.vue'
import orderDetails from './OrderDetail.vue';
export default{
    props:['memberId'],
    data () {
        return {
            infos:[],
            isSwitchOrderDetail:true,
            orderid:''
        }
    },
    created:function(){
       this.$root.$on('detailShow',() => {
            this.isSwitchOrderDetail = true
        });
        setTimeout(() =>{
            this.$root.$emit('load',false);
        },1000);
         this.getOrder();
    },
    methods:{
        jumpOrderDetail (info) {
            this.isSwitchOrderDetail = false
            this.$root.$emit('orderShow',info)
            
        },
        getOrder(){
            let url = '/api/product/order/queryPageList';
               let id = this.memberId
                this.$http({
                        url: url,
                        method: 'post',
                        data: {"memberId":id}
                    })
                    .then(respone => {
                        console.log(respone)
                        this.infos = respone.data.info.list
                        
                    })
                    .catch(error => {
                        console.log(error);
                        //         alert('网络错误，不能访问');
                    })
        }
    },
    components:{
        publicPagination,
        orderDetails,
    },
    beforeDestroy(){
        this.$root.$off('detailShow')
    }
}
</script>
<style scoped lang="less">
#order{
    background: #fff;
    padding:0 25px 20px;
    .orderMain{
            width: 100%;
            padding-bottom: 100px;
        li{
            font-size: 14px;
            color:#666;
            margin:20px 0;
            background:#F2F3F4;
            .orderDiv{      
                padding: 0 45px 0;
                display:flex;
                justify-content: space-between;
                span{
                    display:block;
                }
            }
            .orderDiv:nth-child(1){
                padding-top:20px;
                margin-bottom: 30px;
            }
            .orderDiv:nth-child(2){
                margin-bottom: 20px;
            }
            .orderBtn{
                border-top: 1px solid #dbdbdd;
                padding:10px 0;
                position:relative;
                height: 45px;
                .btnGroup{
                    position: absolute;
                    right: 30px;
                }
                .el-button{
                    background:#fff;
                    color:#409EFF;
                    border-radius:30px;
                }
                .el-button:nth-child(n+2){
                   padding:12px 30px;
                }
                .el-button:hover{
                    background:#409EFF;
                    color:#fff;
                }
            }
        }
    }
}
</style>