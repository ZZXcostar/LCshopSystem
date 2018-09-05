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
                        <el-button type="primary" class="add" @click='addConclusion("no")'>新增</el-button>
                        <el-button class='shuaxin' round><i class='icon iconfont icon-shuaxin'></i></el-button>
                    </div>
                </el-col>
            </el-row>
        </div>
        <conclusionList></conclusionList>
        <addConclusion></addConclusion>
    </el-main>
</template>

<script>
	import conclusionList from "./conclusionList.vue"
	import addConclusion from "./addConclusion.vue"
	export default{
		data(){
			return{
				namepage: '结论管理',
				isAdd:false
			}
		},
		components:{
			conclusionList,
			addConclusion
		},
		methods:{
			addConclusion(){
				this.$root.$emit("addConclusion","no")
			}		
		}
	}
</script>
<style scoped lang="less">
	@import '../subject.less';
</style>