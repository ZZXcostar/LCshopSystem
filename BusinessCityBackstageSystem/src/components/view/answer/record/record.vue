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
			// viewDateil(){
			// 	this.$root.$emit("viewDateil",'no')
			// }
		}
	}
</script>
<style scoped lang="less">
	@import '../subject.less';
</style>