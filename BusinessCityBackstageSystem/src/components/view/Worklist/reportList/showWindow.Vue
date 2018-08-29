<template>
    <el-dialog id="addEstate" :title="ruleForm.textInfo" :before-close="closeDialog" :visible.sync="dialogVisible" width="30%">
        <el-form :model="ruleForm" :rules="rules" ref="ruleForm" label-width="100px" class="demo-ruleForm">
            <el-row>
                <el-col :span='24'>
                    <el-form-item label="模板名称" prop="name">
                        <el-input @change="checkName" v-model.trim="ruleForm.name"></el-input>
                    </el-form-item>
                </el-col>
                 <el-col :span='24'>
                    <el-form-item label="项目阶段">
                        <el-select v-model="houseForm.stage" @change="stageDis" placeholder="请选择">
                            <el-option v-for="(item,index) in stageInfo" :key="index" :label="item.stagename" :value="item.id"></el-option> 
                        </el-select>
                    </el-form-item>
                </el-col>
                <el-col :span='24'>
                    <el-form-item label="适用房型">
                        <el-select v-model="houseForm.roomType" @change="roomTypeDis" placeholder="请选择">
                            <el-option v-for="(item,index) in roomTypeInfo" :key="index" :label="item.typename" :value="item.id"></el-option> 
                        </el-select>
                    </el-form-item>
                </el-col>
                <el-col :span='24'>
                    <el-form-item label="服务类型">
                        <el-select v-model="houseForm.serType" @change="serTypeDis" placeholder="请选择">
                            <el-option v-for="(item,index) in serTypeInfo" :key="index" :label="item.serName" :value="item.id"></el-option> 
                        </el-select>
                    </el-form-item>
                </el-col>
            </el-row>
            <el-form-item class="estate-btn">
                <el-button type="primary" @click="submitForm('ruleForm')">确认</el-button>
            </el-form-item>
        </el-form>
    </el-dialog>
</template>
<style lang="less">
    #addEstate {
        .imgBox {
            width: 100px;
            height: 100px;
            border: 1px solid #ddd;
        }
        .lineBline {
            border-bottom: 1px solid #ddd;
        }
        .estate-btn {
            .el-form-item__content {
                text-align: center;
                margin-left: 0 !important;
            }
        }
        .el-dialog {
            padding-bottom: 5px;
        }
        .citys {
            float: left;
        }
    }
</style>
<script>
    import qs from 'qs'
    export default {
        data() {
            return {
                dialogVisible: false,
                cantSubmit: true,
                bigDisId: {},
                houseForm: {
                    estate: '',
                    smallDistrict: '',
                    stage:null,
                    roomType:null,
                    serType: null,
                    entryTemplateStandards:[],
                    streetMore: '',
                    balcony: null,
                    room: null,
                    hall: null,
                    toilet: null,
                    kitchen: null,
                    square: 0
                },
                serTypeInfo: [],
                roomTypeInfo:[],
                stageInfo: [],
                ruleForm: {
                    textInfo: '新增报告模板',
                    name: "",
                    thisId: ''
                },
                rules: {
                    name: [{
                        required: true,
                        message: '请输入模板名称',
                        trigger: 'blur'
                    }, ]
                }
            }
        },

        methods: {
            searchStageInfo() {
                //  查询阶段信息
                let that = this;
                this.$http.post('/api/public/articles/query',{})
                    .then(res => {
                        console.log(res)
                        this.$root.$emit('load', false);
                        if (res.data.info == null) {
                            alert(res.data.error)
                        } else {
                            that.stageInfo = res.data.info.list;
                            console.log(that.stageInfo)
                        }
                    }).catch(err => {
                        console.log(err)
                    });
            },
            stageDis(val){
                this.houseForm.stage = val?val:this.houseForm.stage;
            },
            searchRoomTypeInfo() {
                //  查询房型信息
                let that = this;
                this.$http.post('/api/public/entrytemplate/type/queryMap',{})
                    .then(res => {
                        console.log(res)
                        this.$root.$emit('load', false);
                        if (res.data.info == null) {
                            alert(res.data.error)
                        } else {
                            that.roomTypeInfo = res.data.info;
                        }
                    }).catch(err => {
                        console.log(err)
                    });
            },
            roomTypeDis(val) {
                this.houseForm.roomType = val?val:this.houseForm.roomType;
                console.log();
            },
            searchServiceInfo() {
                //  查询服务类型信息
                let that = this;
                this.$http.post('/api/product/serviceType/queryList',{})
                    .then(res => {
                        console.log(res)
                        this.$root.$emit('load', false);
                        if (res.data.info == null) {
                            alert(res.data.error)
                        } else {
                            that.serTypeInfo = res.data.info;
                        }
                    }).catch(err => {
                        console.log(err)
                    });
            },
            serTypeDis(val) {
                this.houseForm.serType = val?val:this.houseForm.serType;
                console.log();
            },
            closeDialog(done) {
                this.dialogVisible = false;
                this.$refs['ruleForm'].resetFields();
                this.ruleForm.name = ''
            },
            checkName() {
                if (this.ruleForm.name == '') {
                    this.cantSubmit = true
                    this.$message({
                        message: '模板名不能为空',
                        type: 'warning',
                        duration: 1000
                    })
                } else {
                    this.cantSubmit = false
                }
            },
            submitForm(formName) {
                let addReportData = null;
                let stageName = '';
                if(this.houseForm.stage == null || this.houseForm.stage == ''){
                    this.$message({
                        message: '请选择项目阶段',
                        type: 'warning',
                        duration: 1000
                    })
                    return
                }
                if(this.houseForm.roomType == null || this.houseForm.roomType == ''){
                    this.$message({
                        message: '请选择房屋类型',
                        type: 'warning',
                        duration: 1000
                    })
                    return
                }
                if(this.houseForm.serType == null || this.houseForm.serType == ''){
                    this.$message({
                        message: '请选择服务类型',
                        type: 'warning',
                        duration: 1000
                    })
                    return
                }
                this.stageInfo.forEach((e, i) => {
                    if(e.id == this.houseForm.stage){
                        stageName = e.stagename
                    }
                })
                if (this.ruleForm.textInfo == "新增报告模板") {
                    
                    addReportData = {
                        "name":this.ruleForm.name,
                        "stageId":this.houseForm.stage,
                        "stageName":stageName,
                        "typeId":this.houseForm.roomType,
                        "ServiceTypeId":this.houseForm.serType,
                        "entryTemplateStandards":this.houseForm.entryTemplateStandards
                    }
                    this.$root.$emit('addReportDatas',addReportData);
                }
                if (this.ruleForm.textInfo == "编辑报告模板") {
                     addReportData = {
                        "name":this.ruleForm.name,
                        "stageId":this.houseForm.stage,
                        "stageName":stageName,
                        "typeId":this.houseForm.roomType,
                        "ServiceTypeId":this.houseForm.serType,
                        "entryTemplateStandards":this.houseForm.entryTemplateStandards,
                        "id":this.ruleForm.thisId
                    }
                    this.$root.$emit('addReportDatas',addReportData);
                }
                this.dialogVisible = false;
                this.$refs[formName].resetFields();
            }
        },
        created: function() {
            this.searchStageInfo();
            this.searchRoomTypeInfo();
            this.searchServiceInfo();
            this.$root.$on('showWindowss', (data) => {
                
                this.$nextTick(() => {
                    if (data.type != 'no') {
                        this.houseForm.stage = '',
                        this.houseForm.roomType = '',
                        this.houseForm.serType='',
                        this.dialogVisible = true;

                        this.ruleForm.textInfo = '编辑报告模板';

                        console.log(data.rowData);
                        this.ruleForm.thisId = data.rowData.id
                       this.ruleForm.name = data.rowData.name

                         this.stageDis(data.rowData.stageId)
                         this.roomTypeDis(data.rowData.typeId)
                         this.serTypeDis(data.rowData.serviceTypeId);
                       
                    //    this.houseForm.stage = data.rowData.stagename;
                    //    this.houseForm.roomType = data.rowData.roomType,
                    //     this.houseForm.serType=data.rowData.serType;
                         this.houseForm.entryTemplateStandards = data.rowData.entryTemplateStandards
                    } else {
                        this.dialogVisible = true;
                        this.ruleForm.textInfo = '新增报告模板';
                        this.ruleForm.name = ""
                        this.houseForm.stage = ""
                        this.houseForm.roomType = ''
                        this.houseForm.serType=''
                        this.houseForm.entryTemplateStandards = []
                    }
                })
            });
        },
        beforeDestroy() {
            this.$root.$off('showWindowss');
        }
    }
</script>