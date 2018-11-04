<template>
    <el-dialog title="发优惠券" :visible.sync="dialogFormVisible">
        <el-form :model="form">
            <el-form-item label="您选中的会员:" :label-width="formLabelWidth"><span>{{couponName}}</span>&nbsp;位</el-form-item>
            <el-row>
                <el-col :span='12'>
                    <el-form-item label="优惠券" :label-width="formLabelWidth">
                        <el-select v-model="form.tages" placeholder="请选择" @change='changes'>
                            <el-option v-for=' (item,index) in dynamicTags' :key="index" :label='item.name' :value="item.id"></el-option>
                        </el-select>
                    </el-form-item>
                </el-col>
            </el-row>
            <el-row>
                <el-col :offset="1" style='padding-bottom:10px;'>
                    <el-tag :key="tag.name" v-for="(tag,index) in Tagbox" style='background-color: #409EFF;color:#fff;margin-right:10px;margin-bottom:10px; ' closable :disable-transitions="false" @close="handleCloses(index)">
                        {{tag.name}}
                    </el-tag>
                </el-col>
            </el-row>
        </el-form>
        <div slot="footer" class="dialog-footer">
            <el-button @click="dialogFormVisible = false">取 消</el-button>
            <el-button type="primary" @click="submitForm('form')">确 定</el-button>
        </div>
    </el-dialog>
</template>

<script>
    export default {
        data() {
            return {
                dialogFormVisible: false,
                dynamicTags: [],
                Tagbox: [],
                TagboxId: [],
                propleId: [],
                NewArr:[],
                couponName: '',
                form: {
                    tages: '',
                    name: '',
                    region: '',
                    thisId: ''
                },
                formLabelWidth: '120px',
            };
        },
        created() {
            this.searchCoupon()
            this.$root.$on("memberCoupon", (data) => {
                this.dialogFormVisible = data[0].isShow
                this.couponName = data[0].list.length;
                data[0].list.forEach((item, index) => {
                    this.propleId.push(item.id)
                })
            })
        },
        methods: {
            submitForm(formName) {
                let that = this;
                let lal = {};
                        this.NewArr = [];
                        
                        for(let i = 0;i<this.propleId.length;i++){
                            for(let j = 0;j<this.TagboxId.length;j++){
                                 lal = {'customerId':this.propleId[i],'couponId':this.TagboxId[j].labelId,'number':1}
                                this.NewArr.push(lal)
                                console.log(this.NewArr)
                            }
                        }

                  
                            let url = '/api/product/coupon/customer/mall/bulkInsert';
                            this.$http({
                                    url: url,
                                    method: 'POST',
                                    // 请求体重发送的数据
                                    headers: {
                                        'Content-Type': 'application/json'
                                    },
                                    data:this.NewArr
                                })
                                .then(response => {
                                    if (response.data.status == 300) {
                                        this.$message(response.data.msg);
                                        // alert(response.data.msg)
                                        
                                        return false
                                    }
                                    this.option('成功发出优惠券','success');
                                    this.Tagbox = [];
                                    this.NewArr = [];
                                    this.dialogFormVisible = false;
                                    this.$root.$emit('getDatezdy', 1)
                                })
                                .catch(error => {
                                    console.log(error);
                                    //         alert('网络错误，不能访问');
                                })
                       
            },
            option(test, status) {
                this.$message({
                    message: test,
                    type: status ? status : 'warning'
                })
            },
            searchCoupon() {
                let url = '/api/product/coupon/info/find?pageSize=0';
                this.$http({
                        url: url,
                        method: 'post',
                        data: {}
                    })
                    .then(respone => {
                        console.log(respone)
                        for (let i = 0; i < respone.data.info.list.length; i++) {
                            this.dynamicTags.push({
                                name: respone.data.info.list[i].couponName,
                                id: respone.data.info.list[i].id
                            })
                        }
                    })
                    .catch(error => {
                        console.log(error);
                        //         alert('网络错误，不能访问');
                    })
            },
            changes(value) {
                let obj = {};
                let newObj = {};
                obj = this.dynamicTags.find((item) => {
                    if (item.id === value) {
                        if (value !== "" || value !== null) {
                            newObj = {
                                labelId: item.id,
                                accountId: this.form.thisId
                            }
                            this.TagboxId.push(newObj)
                        }
                    }
                    return item.id === value;
                });
                this.Tagbox.push(obj);
                //过滤重复项
                var hash = {};
                this.Tagbox = this.Tagbox.reduce(function(item, next) {
                    hash[next.name] ? '' : hash[next.name] = true && item.push(next);
                    return item
                }, [])
                //过滤id
                var hashId = {};
                this.TagboxId = this.TagboxId.reduce(function(item, next) {
                    hashId[next.labelId] ? '' : hashId[next.labelId] = true && item.push(next);
                    return item
                }, []);
            },
            handleCloses(index) {
                let list = [];
                for (let i = 0; i < this.Tagbox.length; i++) {
                    if (index != i) {
                        list.push(this.Tagbox[i]);
                        this.$message({
                            type: 'success',
                            message: '删除成功！'
                        });
                    }
                }
                this.Tagbox = list;
                console.log(this.Tagbox)
            },
        },
        beforeDestroy() {
            this.$root.$off('MarkLable');
        }
    };
</script>
<style>

</style>