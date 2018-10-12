<template>
	<el-main id="answer">
        <div class="memberNav" @click.native="closeInfo">
            <el-row class="navChild">
                <el-col :span='20' class="topLeft">
                    <h3 class="sublistName">{{namepage}}
		                <i class="icon-double-angle-right"></i>
		            </h3>
                </el-col>
                <el-col :span='4' class="topRight">
                    <div class="grid-content search">
                        <el-button class='shuaxin' round><i class='icon iconfont icon-shuaxin' ></i></el-button>
                    </div>
                </el-col>
            </el-row>
        </div>
        <recordList></recordList>
        <addRecord></addRecord>
    </el-main>
</template>

<script>
	import recordList from './recordList.vue'
	import addRecord from './addRecord.vue'
	export default{
		data(){
			return{
				namepage: '答题记录',
				isAdd:false
			}
		},
		components:{
			recordList,
			addRecord
		},
		created(){
			
		},
		methods:{
			viewDateil(){
				this.$root.$emit("viewDateil",'no')
			}
		}
	}
</script>
<style>
	#answer .lineHeight {
        position: relative;
        top: 24px;
        color: #6f6f6f;
    }

    #answer table td:nth-child(3) {
        padding-right: 10px;
    }
    #answer .icon-shuaxin1 {
        position: absolute;
        top: -4px;
    }
    #answer .icon-sousuo {
        position: absolute;
        top: -9px;
        left: 359px;
    }
    #answer .add {
        background: #27a1f2;
        border: 1px solid #27a1f2;
    }
    #answer #file-upload .el-upload{
        width: 100% !important;
        text-align: left;
    }
    #answer .outPut a{
        display:inline-block;
        width: 100% !important;
        text-align: left;
    }
    #answer .shuaxin{
    	border: 0px;
    	font-size: 40px;
        padding: 0;
        width: 10%;
        margin-left: 10px;
        background: none;
        padding-top: 5px;
        height: 37px;
    }
    #answer .shuaxin:hover{
    	background: none;
    }
    #answer .add {
        font-size: 16px;
        height: 37px;
        line-height: 0;
        margin-left: 15px;
    }
    #answer .topRight{
    	float: right;
    	height: 60px;
    }
    #answer .sublistName{
    	height: 20px;
    	padding-left: 20px;
    	font-size: 20px;
    	color: #0D4156;
    	border-left: 4px solid #253a4d;
    	margin-left: 33px;
    }
    #answer .topLeft{
    	padding-top: 25px;
    }
</style>
<style scoped lang="less">
	@import '../subject.less';
</style>