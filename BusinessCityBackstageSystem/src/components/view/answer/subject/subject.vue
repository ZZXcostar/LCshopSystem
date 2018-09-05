<template>
	<el-main id="member">
        <div class="memberNav" @click.native="closeInfo">
            <el-row class="navChild">
                <el-col :span='20' class="topLeft">
                    <h3 class="listName">{{namepage}}
		                <i class="icon-double-angle-right"></i>
		            </h3>
                </el-col>
                <el-col :span='4' class="topRight">
                    <div class="grid-content search">
                        <el-button type="primary" class="add" @click='isadd("no")'>新增</el-button>
                        <el-button class='shuaxin' round><i class='icon iconfont icon-shuaxin'></i></el-button>
                    </div>
                </el-col>
            </el-row>
        </div>
        <subjectList></subjectList>
        <addSubject></addSubject>
    </el-main>
</template>

<script>
	import subjectList from './subjectList';
	import addSubject from './addsubject';
	export default{
		data(){
			return{
				namepage: '答题管理'
			}
		},
		components:{
			addSubject,
			subjectList
		},
		methods:{
			isadd(){
                this.$root.$emit("isadd",'no')
			}
		}
	}
</script>

<style>
	.lineHeight {
        position: relative;
        top: 24px;
        color: #6f6f6f;
    }

    table td:nth-child(3) {
        padding-right: 10px;
    }
    .icon-shuaxin1 {
        position: absolute;
        top: -4px;
    }
    .icon-sousuo {
        position: absolute;
        top: -9px;
        left: 359px;
    }
    .add {
        background: #27a1f2;
        border: 1px solid #27a1f2;
    }
    #file-upload .el-upload{
        width: 100% !important;
        text-align: left;
    }
    .outPut a{
        display:inline-block;
        width: 100% !important;
        text-align: left;
    }
    .shuaxin{
    	border: 0px;
    	font-size: 40px;
        padding: 0;
        width: 10%;
        margin-left: 10px;
        background: none;
        padding-top: 5px;
        height: 37px;
    }
    .shuaxin:hover{
    	background: none;
    }
    .add {
        font-size: 16px;
        height: 37px;
        line-height: 0;
        margin-left: 15px;
    }
    .topRight{
    	float: right;
    	height: 60px;
    }
    .listName{
    	height: 20px;
    	padding-left: 20px;
    	font-size: 20px;
    	color: #0D4156;
    	border-left: 4px solid #253a4d;
    	margin-left: 33px;
    }
    .topLeft{
    	padding-top: 25px;
    }
</style>
<style scoped lang="less">
	@import '../subject.less';
</style>