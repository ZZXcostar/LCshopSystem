<template>
<div>
    <el-table
    :data="datalist"
    :border="isBorder"
    @selection-change='showextra'
    @cell-click='showMemberInfo'
    :default-sort = "{prop: 'date', order: 'descending'}"
    v-loading="this.listLoading"
    :stripe='true'
    ref="moviesTable"
    style="width: 100%"
    height='500'>
    <el-table-column
    fixed
    type="index"
    label="N"
    :index="indexMethod">
    </el-table-column>
        <el-table-column
        fixed
        type="selection" >
        </el-table-column>
        <el-table-column
        prop="itemname"
        label="检查项"
        >
        </el-table-column>
        <el-table-column
        prop="point"
        label="检查点">
        </el-table-column>
        <el-table-column
        prop="standardname"
        label="标准">
        </el-table-column>
        <el-table-column
        prop="acceptance"
        label="验收方法">
        </el-table-column>
        <el-table-column
        label="必填">
        <template slot-scope="scope">
            <el-checkbox v-model="scope.row.checked" @change="selecteChange(scope.row)"></el-checkbox>
        </template>
        </el-table-column>
    </el-table>
    <el-row class='btnsgroup'>
        <el-button @click.native="canleClick">取消</el-button>
        <el-button type="primary" @click.native="savedata">保存</el-button>
    </el-row>
 </div>
</template>
<script>
/* eslint-disable */
//@row-click="showMemberInfo()"
export default {
    prop:['listLoading'],
    data(){
        return {
            isBorder:false,
            listLoading: false,
            datalist:[],
            showLeft:0,
            pageIndex:1,
            addReportData:'',
            multipleData:'',
            submitData:'',
            isAdd:true
        }
    },
    created:function(){
       this.$root.$on('addReportDatas',(data) => {
           this.addReportData = data
           this.getDate()
           this.multipleData = '';
       });
    },
    methods:{
        getDate() {
            this.listLoading =  true;
            let that = this;
            let url = '/api/public/articles/queryall?page=0&pageSize=0';
            this.$http({
                url: url,
                method: 'post',
                // 请求体重发送的数据
                // headers: { 'Content-Type': 'application/json' },
                data: {"id":this.addReportData.stageId},
            })
            .then(response => {
                this.listLoading =  false;
                let dataListss = (response.data.info.list)
                dataListss.forEach((e,i) => {
                    e.checked = false
                });
                this.datalist= dataListss;
                console.log(this.datalist)

                console.log(this.addReportData.entryTemplateStandards)
                if(this.addReportData.entryTemplateStandards.length > 0 ){
                    this.isAdd = false;
                    let standardIdArr = [];
                   this.addReportData.entryTemplateStandards.forEach(e=>{
                       let obj = {
                           "standardId":e.standardId,
                           "entryType":e.entryType
                       }
                        standardIdArr.push(obj)
                    })
                    console.log(this.datalist)
                        this.$nextTick(function () {
                             this.datalist.forEach((e,i) => {
                                    standardIdArr.forEach((a,j) =>{
                                            if(e.standardId == a.standardId){
                                                this.datalist[i].checked = standardIdArr[j].entryType
                                                this.$refs.moviesTable.toggleRowSelection(e);
                                            }
                                    })
                            
                                });
                        })
                }
          })
          .catch(error=>{
              console.log(error);
              //         alert('网络错误，不能访问');
          })
        },
        showMemberInfo(row,column,cell,event){//  点击显示侧滑
            //console.log(row,column,cell,event)
            //  let classNum = cell.className.split('n_')[1] //  获取单元格的类名
            // let labelValue = column.label
            // if(labelValue == 'ID'){
            //      this.$root.$emit('searchPersonnelInfo',row.id)
            // }
        },      
        showextra(val){
             this.multipleData = val
             console.log(this.multipleData)
             let arr = this.multipleData;
             let newArr = []
             arr.forEach(item => {
                 let obj = {
                 "entryType":'',
                 "standardId":''
               }
               obj.standardId = item.standardId
               obj.entryType = item.checked
               newArr.push(obj)
             })
             console.log(newArr)
             this.submitData = newArr
         //方法二    
        //    let arr = this.multipleData;
        //      arr.map(item => {
        //        item.entryType = item.checked;
        //        delete item.acceptance;
        //            delete item.acceptance;
        //            delete companyId
        //            delete firstRow
        //            delete id
        //            delete itemId 
        //            delete itemname
        //            delete labelList
        //            delete orderBy
        //            delete pageNum 
        //            delete pageSize 
        //            delete point 
        //            delete pointId
        //            delete rowSize 
        //            delete stagename
        //            delete standardname
        //            return item; 
        //      })
        //     console.log(arr)
        },
        selecteChange(val){
            console.log(val)
            if( this.submitData == ''){
                this.$message({
                    type: 'info',
                    message: '还未勾选报告',
                    duration:800
                    });
                    return
            }
             let id = val.standardId
             let checked = val.checked
            this.submitData.forEach((e,i)=>{
               if(id == e.standardId){
                   e.entryType = checked
               } 
            })
            console.log(this.submitData)
        },
        indexMethod(index) {
            return index + 1
        },
        canleClick(){
            this.$root.$emit('bindingIsShow',true);
        },
        savedata(){
            if(this.submitData == ''){
                alert('请勾选报告！')
                return
            }
            let that = this;
            let datas = null;
            let url = '';
            if(this.addReportData.id){
                 url = '/api/public/entrytemplate/update';
                 datas = {
                    'name':this.addReportData.name,
                    'stageId':this.addReportData.stageId,
                    'typeId':this.addReportData.typeId,
                    'serviceTypeId':this.addReportData.ServiceTypeId,
                    'entryTemplateStandards':this.submitData,
                    'id':this.addReportData.id
                 }
            }else{
                
                url = '/api/public/entrytemplate/insertOne';
              datas = {
                    'name':this.addReportData.name,
                    'stageId':this.addReportData.stageId,
                    'typeId':this.addReportData.typeId,
                    'serviceTypeId':this.addReportData.ServiceTypeId,
                    'entryTemplateStandards':this.submitData
                 }
            }
            
             this.$http({
                url: url,
                method: 'post',
                // 请求体重发送的数据
                // headers: { 'Content-Type': 'application/json' },
                data: datas,
            })
            .then(response => {
                //  (response.data.info.list)
                console.log(response.status)
                if(response.status == 200){
                    that.$message({
                    type: 'info',
                    message: '保存成功',
                    duration:800
                    });
                    that.$root.$emit('bindingIsShow',true);
                    that.$root.$emit('getDatezbd',1);
                }else{
                    that.$message({
                    type: 'info',
                    message: '保存失败',
                    duration:800
                    });
                }
          })
          .catch(error=>{
              console.log(error);
              //         alert('网络错误，不能访问');
          })
        }
    },
    beforeDestroy(){
        this.$root.$off('addReportDatas');
    }
}
</script>
<style scoped>
</style>

