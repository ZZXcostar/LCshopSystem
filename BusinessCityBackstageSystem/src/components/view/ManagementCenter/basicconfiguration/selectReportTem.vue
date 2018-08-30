<template>
      <el-dialog
        title="报告模板"
        :visible.sync="dialogVisibless"
        width="52%"
        id="activeDialogZbd"
        >
            <!-- <el-row style='padding-bottom:10px;' :gutter='24'>
                <el-col :span="6">
                    <div class='typeselect'>
                        <el-input
                            placeholder="输入相关标题"
                            v-model="name"
                            >
                        </el-input>
                    </div>
                </el-col>
                <el-col :span='3'>
                    <el-button size="mini" class='createservice' @click="nameSelect">搜索</el-button>
                </el-col>
            </el-row> -->
            <el-table 
                :data="listgoods" 
                ref='goodtable' 
                style="width: 100%;overflow-y: scroll;" 
                class='table-products' 
                @select="selectChange"
                v-loading='loading'>
                    <el-table-column
                    fixed
                    type="selection" 
                    width="55">
                    </el-table-column>
                    <el-table-column  
                    prop="name" 
                    label="报告模板名称"  >
                    </el-table-column>
                    <el-table-column
                    prop="entryStage.stagename"
                    label="阶段">
                    <template slot-scope="scope">
                        <span >{{ scope.row.entryStage == null?'无此信息':scope.row.entryStage.stagename }}</span>
                    </template>
                    </el-table-column>
                    <el-table-column
                    prop="entryTemplateType.typename"
                    label="适用房型">
                     <template slot-scope="scope">
                        <span >{{ scope.row.entryTemplateType== null?'无此信息':scope.row.entryTemplateType.typename }}</span>
                    </template>
                    </el-table-column>
                    <el-table-column
                    label="状态">
                    <template slot-scope="scope">
                        <span :class="scope.row.isService == true?'templateStausColorGreen':scope.row.isService == false?'templateStausColorRed':''">{{ scope.row.isService?"启用":"未启用" }}</span>
                    </template>
                    </el-table-column>
                    <!-- <el-table-column
                    prop="createTime"
                    label="创建时间"
                    width='170'>
                    </el-table-column> -->
                     <el-table-column
                    prop="serviceType.serName"
                    label="适用服务类型">
                    </el-table-column>
                </el-table>
                <el-pagination
                @current-change="handleCurrentChange"
                :current-page.sync="currentPage"
                :page-size="pages"
                layout="prev, pager, next, jumper"
                :total="total"
                style="text-align: right;
                position: absolute;
                bottom: 2%;
                right: 2%;
                top: 88%;">
                </el-pagination>
                <div class="zbd-selectedTip" v-show="selectedDiv">已选择：<span :style="tipcolor">{{ selectedTitle }}</span></div>
            <span slot="footer" class="dialog-footer">
                <el-button @click="dialogVisibless = false">取 消</el-button>
                <el-button type="primary" :disabled="disabled" @click="saveTemplateID">确 定</el-button>
            </span>
        </el-dialog>
</template>
<script>
  export default {
       data() {
        return {
            dialogVisibless: false,
            reportIndex:'',
            disabled:false,
            selectedDiv:false,
            tipcolor:{
                color:""
            },
            selectedTitle:'',
            currentPage:1,
            total:1,
            pages:1,
            type:'',
            typeid:'',
            listgoods:[],
            loading:true,
            input:'',
            proTemplate:'',
             urlList:[
                {id:1,url:'/api/public/EntryReportTemplate/queryMapByIds',des:'监理-公寓',name:'stage'},
                {id:2,url:'/api/public/EntryReportTemplate/queryMapByIds',des:'监理-复式',name:'stage'},
                {id:3,url:'/api/public/EntryReportTemplate/queryMapByIds',des:'监理-别墅',name:'stage'},
                {id:4,url:'/api/public/EntryReportTemplate/queryByIds',des:'陪签服务',name:'stage'}
            ],
            zbdindex:''
        };
    },
    methods: {
       getDatalist(data){
                let that=this;
                let pageindexs = data.pageindex;
                let serviceTypeId = data.serviceTypeId;
                let stageId = data.stageId
                let typeId = null;
                let subData = null;
                 if(data.typeId == null || data.stageId == null){
                     //陪签data
                   subData = {
                        'serviceTypeId':serviceTypeId,
                        'isService':true
                    }
                }else{
                     typeId = data.typeId;
                     //监理data
                    subData = {
                        "stageId":stageId,
                        'serviceTypeId':serviceTypeId,
                        'typeId':typeId,
                        'isService':true
                    }
                }
                this.loading=true;
                this.$http.post('/api/public/entrytemplate/query?page='+pageindexs+'&pageSize=10',subData
                               
                )
                .then(function (response) {
                    console.log(response);
                    if(response.data.msg=='查询成功'){
                        let data=response.data.info;
                        that.listgoods=data.list;
                        that.pages=data.pageSize;
                        that.total=data.total;
                        that.currentPage=data.pageNum;
                    }
                    else{
                        that.$message(response.data.msg);
                    }
                    console.log(response);
                    that.loading=false;
                    
                })
                .catch(function(response){
                    that.$message(response);
                    that.loading=false;
                });
            },
            handleCurrentChange(currentPage){
                let datas = {
                    "stageId":data.datas.stageId,
                    "serviceTypeId":data.needId.serviceTypeId,
                    "typeId":data.needId.typeId,
                    "pageindex":currentPage
                }
                this.getDatalist(datas);
            },
            selectChange(val){
                console.log(val)
                if(val.length > 1){
                    this.disabled = true;
                    this.selectedDiv = true;
                    this.selectedTitle = '请选择一个模板';
                    this.tipcolor.color = "#ff3b30";
                    this.input = ''
                }else if(val.length == 1){
                    this.disabled = false;
                    this.selectedDiv = true
                    this.selectedTitle = val[0].name
                    this.input = val[0].id
                    console.log(val[0].name)
                    console.log(val[0].id)
                    this.tipcolor.color = "#50c380";
                }else{
                    this.input = ''
                    this.selectedDiv = false
                }
            },
            saveTemplateID(){
                if(this.input == ''){
                    this.$message({
                        type: 'info',
                        message: '请选择一个模板'
                    });
                    return false
                }else{
                    //保存报告模板
                    let that = this;
                    let sureData = {
                        "id": this.reportId,
                        "entryTemplateId": this.input
                    }
                    this.$http.post('/api/public/EntryReportTemplate/update',sureData)
                    .then(function (response) {
                        console.log(response);
                        if(response.data.status == 200){
                             that.$message({
                                type: 'info',
                                message: '添加成功'
                            });
                            that.selectedDiv = false;
                            that.selectedTitle = '';
                            that.dialogVisibless = false;
                             that.$root.$emit('saveReportTemplateInfos', {
                                "indexs":that.zbdindex,"url":that.urlList[that.zbdindex-1].url,'zbdid':that.urlList[that.zbdindex-1].id
                            })
                            
                        }else{
                            that.$message(response.data.msg);
                            that.loading=false;
                        }
                    })
                    .catch(function(response){
                        that.$message(response);
                        that.loading=false;
                    });
                }
          },
    },
    created:function(){
            this.$root.$on('opendialogSelectReport',(data) =>{
                this.reportId = '';
                this.input = '';
                this.dialogVisibless = data.isShow
                this.reportIndex = data.indexs
                this.reportId = data.datas.id
                this.zbdindex = data.zbdindex
                let datas = {
                    "stageId":data.datas.stageId,
                    "serviceTypeId":data.needId.serviceTypeId,
                    "typeId":data.needId.typeId,
                    "pageindex":1
                }
                this.getDatalist(datas);
            });
            
      },
        beforeDestroy(){
            this.$root.$off('opendialogSelectReport');
        }
  }
</script>
<style>

#activeDialogZbd .el-dialog__footer{
    padding-top:50px;
}
</style>
<style scoped lang="less">
.templateStausColorGreen{
    color:#50c380;
}
.templateStausColorRed{
    color:#ff3b30
}
.imgBox {
        width: 100px;
        height: 100px;
        border: 1px solid #ddd;
    }
    .lineBline {
        border-bottom: 1px solid #ddd;
    }
    .el-table{
        border-top: 6px solid #56d2f4;
    }
    .el-pagination{
        /* margin-top: 0px; */
        text-align: right;
        position:absolute;
        bottom:2%;
        right:2%;
    }
</style>
<style>
    .typeselect .el-input__inner{
        height: 30px;
    }

    .el-dialog__wrapper{
        width:100%;
        height:100%;
    }
    .commoditydialog .el-dialog{
        height:80%;
        padding-bottom:0px;
    }
    .commoditydialog .el-dialog__body{
        height:75%;
        padding-top:10px;
    }
    .table-products tr td:nth-child(2){
        padding:0;
        text-align:center;
    }
    /* .table-product tr td:nth-child(2) .cell{
       width:70px;
        height:40px;
        margin:0 auto;
        padding:0;
        line-height:10px;
        background-color:#ebeef5;
    } */
    .zbd-selectedTip{
        position: absolute;
            top: 20%;
        right: 87%;
        width: 285px;
        height: 26px;
        text-align: right;
        overflow: hidden;
    }
</style>
