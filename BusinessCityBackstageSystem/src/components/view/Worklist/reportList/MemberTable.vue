<template>
    <el-table
    :data="datalist"
    :border="isBorder"
    @selection-change='showextra'
    @cell-click='showMemberInfo'
    :default-sort = "{prop: 'date', order: 'descending'}"
    v-loading="this.listLoading"
    :stripe='true'
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
        <!-- <el-table-column class='borderRight' fixed prop="id" label="ID" width='360' height='100'>
        </el-table-column> -->
        <el-table-column
        prop="name"
        label="报告模板名称"
        >
        </el-table-column>
        <el-table-column
        prop="entryStage.stagename"
        label="阶段">
        </el-table-column>
        <el-table-column
        prop="entryTemplateType.typename"
        label="适用房型">
        </el-table-column>
        <el-table-column
        label="状态">
        <template slot-scope="scope">
            <span :class="scope.row.isService == true?'templateStausColorGreen':scope.row.isService == false?'templateStausColorRed':''">{{ scope.row.isService?"启用":"未启用" }}</span>
        </template>
        </el-table-column>
        <el-table-column
        prop="serviceType.serName"
        label="适用服务类型">
        </el-table-column>
        <el-table-column
        prop="types"
        label="操作">
         <template slot-scope="scope">
            <el-button type="text"  size="small" @click="handleEdit(scope.$index, scope.row,$event)">编辑</el-button>
            <el-button type="text" size="small" @click="handleDelete(scope.$index, scope.row,$event)">删除</el-button>
        </template>
        </el-table-column> 
    </el-table>
</template>
<script>
/* eslint-disable */
//@row-click="showMemberInfo()"
export default {
    prop:['listLoading'],
    data(){
        return {
            isBorder:false,
            datalist:[],
            showLeft:0,
            pageIndex:1
        }
    },
    created:function(){
        this.$root.$emit('output',"");
        this.$root.$on('pageIndex',(data) => {
            this.pageIndex = data.value
            this.getDate(this.pageIndex,{})
        })
        this.getDate(1,{})
        this.$root.$on('getDatezbd',(data)=>{
             this.getDate(data,{});
        })
        this.$root.$on('dataListBox',(data)=>{
            this.datalist = data
        })
        this.$root.$on('search',(datas)=>{
            let data={
                name:datas.name,
                alias:datas.alias
            };
            // console.log(data);
            this.getDate(1,data);
        })
    },
    methods:{
        handleDelete(index, row,event) { //  删除某一种产品
            let that = this;
            console.log(row);
            // if(row.isService){
            //     alert('改报告模板正在使用')
            // }
            this.$confirm('确定删除 "'+row.name+'"吗?', '提示', 
                {confirmButtonText: '确定',cancelButtonText: '取消',type: 'warning'})
            .then(() => {
                that.$message({
                    type: 'success',
                    message: '删除成功!',
                    duration:800,
                    onClose:that.$http.post('/api/public/entrytemplate/removeByIds',
                        [row.id]
                    ).then(res => {
                        console.log(res.data.msg);
                        that.$message({
                            type: 'info',
                            message: res.data.msg,
                            duration:800
                        });   
                        that.getDate(1,{});
                    }).catch(err => {console.log(err)})
                });
            }).catch(() => {
                that.$message({
                    type: 'info',
                    message: '已取消删除',
                    duration:800
                });          
            });   
        },
        handleEdit(index, row,event) {
            this.$root.$emit('showWindowss',{type:'yes',rowData:row});
        },
        getDate(pageIndex,data) {
            this.listLoading =  true;
            let url = '/api/public/entrytemplate/query?pageNum='+(pageIndex?pageIndex:1)+'&pageSize=10'
            let datas = data;
            let dataz;
            if(JSON.stringify(datas)=="{}"){
                dataz = {};
            }else{
                dataz = {
                    "name":datas.name
                }
            }
            this.$http({
                url: url,
                method: 'post',
                // 请求体重发送的数据
                // headers: { 'Content-Type': 'application/json' },
                data: dataz,
            })
            .then(response => {
                this.listLoading =  false;
                this.datalist=(response.data.info.list);
                console.log(response.data)
                
                this.$root.$emit('pages',response.data.info.pages)
                this.$root.$emit('total',response.data.info.total)
          })
          .catch(error=>{
              console.log(error);
              //         alert('网络错误，不能访问');
          })
        },
        showMemberInfo(row,column,cell,event){//  点击显示侧滑
            //console.log(row,column,cell,event)
            //  let classNum = cell.className.split('n_')[1] //  获取单元格的类名
            let labelValue = column.label
            if(labelValue == 'ID'){
                this.showLeft = 16
                this.$root.$emit('infoCoverShow',this.showLeft)
                this.$root.$emit('searchPersonnelInfo',row.id)
            }
        },      
        showextra(val){
             let show=false;
             let editcan=true;
             this.multipleSelection = val
             this.$root.$emit('output',val);
            if(this.multipleSelection.length>0){
                show=true;
            }
            if(this.multipleSelection.length>1){
                editcan=false;
            }
             this.$root.$emit('showlttip',{show,editcan,num:this.multipleSelection.length,datas:this.multipleSelection});
        },
        indexMethod(index) {
            return index + 1
        },
    },
    beforeDestroy(){
        this.$root.$off('pageIndex');
        this.$root.$off('getDatezbd');
        this.$root.$off('dataListBox');
        this.$root.$off('search');
    }
}
</script>
<style scoped>
.templateStausColorGreen{
    color:#50c380;
}
.templateStausColorRed{
    color:#ff3b30
}
</style>

