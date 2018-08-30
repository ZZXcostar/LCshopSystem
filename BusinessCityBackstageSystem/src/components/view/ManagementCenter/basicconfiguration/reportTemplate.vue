<template>
    <div id="supplier-model">
        <h5>配置类型</h5>
        <ul class="basic-content">
            <li v-for="(info,index) in typeData" :key="index" @click="findInfo(index,urlList[index].url,urlList[index].id)">
                <el-button type="primary" :class="{active:info.isActive}" :data-id="index" @click="handleBtn($event)" plain><i :class="info.icon"></i>{{info.name}}</el-button>
            </li>  
        </ul>
    </div>
</template>
<script>
export default {
    data () {
        return {
            typeData:[
                {'index':1,'icon':'el-icon-menu','isActive':true,'name':'监理-公寓'},
                {'index':2,'icon':'el-icon-menu','isActive':false,'name':'监理-复式'},
                {'index':3,'icon':'el-icon-menu','isActive':false,'name':'监理-别墅'},
                {'index':4,'icon':'el-icon-sold-out','isActive':false,'name':'陪签服务'}
            ],
            urlList:[
                {id:1,url:'/api/public/EntryReportTemplate/queryMapByIds',des:'监理-公寓',name:'stage'},
                {id:2,url:'/api/public/EntryReportTemplate/queryMapByIds',des:'监理-复式',name:'stage'},
                {id:3,url:'/api/public/EntryReportTemplate/queryMapByIds',des:'监理-别墅',name:'stage'},
                {id:4,url:'/api/public/EntryReportTemplate/queryByIds',des:'陪签服务',name:'stage'}
            ],
            datalist:[]
        }
    },
    created(){
        this.getDate(0,this.urlList[0].url,this.urlList[0].id);
        
        this.$root.$on('saveReportTemplateInfos',(data) =>{
                let index = data.indexs
                let i = data.url
                let id = data.zbdid
                this.getDate(index,i,id);
            });
    },
    methods:{
        handleBtn(event){
            this.typeData.forEach((e,i) => {
                e.isActive = false;
            })
            let index = event.currentTarget.getAttribute("data-id");
            this.typeData[index].isActive = true;
        },
        findInfo(index,i,id){
            this.$root.$emit('loadInfo',true);
            this.getDate(index,i,id);
        },
        getDate(index,i,id) {
            let datas = [];
            datas.push(id)
            let that = this;
            this.$http.post(i, datas)
                    .then(function(response) {
                        console.log(response);
                            that.datalist=(response.data.info);
                            console.log(that.datalist)
                            that.$root.$emit('reportInfoSetting',{"datalist":that.datalist,"index":id})
                        // this.$root.$emit('searchInfo',[this.urlList[index].name,this.datalist,index,this.urlList[index].id,this.urlList[index].des]);
                        //获取数据给列表
                            that.$root.$emit('loadInfo',false);
                    })
                    .catch(function(error) {
                            console.log(error);
                    });
        }
    }
    // ,
    // beforeDestroy(){
    //     this.$root.$off('transFn4');
    // }
}
</script>
<style lang="less">
#supplier-model{
    .el-button--primary.is-plain.active{
        background:#409EFF;
        span{
            color:#fff !important;
        }    
    }
}
</style>
<style scoped lang="less">
#supplier-model{
    h5{
        color:#8E8E8E;
        font-size:16px;
        padding:15px 0;
        margin-left:30px;
    }
    .add-btn{
        padding:5px 15px;
        float: right;
        margin-right:20px;
        margin-top:-30px;
        border-radius:5px;
    }
    .basic-content{
        padding-top:30px;
        border-top:1px solid #f4f4f4;
    }
    .basic-content{
        display:flex;
        justify-content:flex-start;
        flex-wrap:wrap;
        li{
            width: 30%;
            margin:0 0 20px 2.5%;
            .el-button--primary.is-plain{
                width: 100%;
                height:120px;
                padding:0 0;
                background:#fff;
                i{
                    width: 50px;
                    height:50px;
                    background:#409EFF;
                    border-radius:5%;
                    text-align:center;
                    line-height:50px;
                    font-size:30px;
                    margin-right:10px;
                    color:#fff;
                    vertical-align: middle;
                }
            }
            .el-button--primary.is-plain:hover{
                background:#409EFF;
                i{
                    background:#fff;
                    color:#409EFF;
                }
            }
            .el-button--primary.is-plain.active{
                background:#409EFF;
                i{
                    background:#fff;
                    color:#409EFF;
                }
            }
        }
    }
}
</style>

