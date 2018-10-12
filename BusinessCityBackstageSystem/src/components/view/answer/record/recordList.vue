<template>
	<div class="recordlist">
		<el-table :data="tableData" stripe style="width: 100%">
			<el-table-column type="index" width="50">
			</el-table-column>
			<el-table-column type="selection" width="55">
			</el-table-column>
			<el-table-column prop="customerPhone" label="手机号" width="180">
			</el-table-column>
			<el-table-column prop="createTime" label="答题时间" width="180">
			</el-table-column>
			<el-table-column prop="num" label="答对数量">
			</el-table-column>
			<el-table-column prop="resultTitle" label="装修称号">
			</el-table-column>
			<el-table-column label="操作">
				<template slot-scope="scope">
					<el-button type="text" size="small" data-index="index" @click="viewDateil(scope.row)">查看详情</el-button>
				</template>
			</el-table-column>
		</el-table>
		<div class="block">
			<el-pagination :current-page.sync="currentPage1" @current-change="page" :page-size="10" layout="total, prev, pager, next" :total="total">
			</el-pagination>
		</div>
	</div>
</template>

<script>
	export default {
		data() {
			return {
				tableData: [{
					phone: '15268095284',
					date: '2018-08-21 12:00',
					num: 1,
					name: "装修小渣渣"
				}],
				currentPage1: 5,
				total:0
			}
		},
		created(){
			this.page(1)
		},
		methods:{
			viewDateil(row){
				this.$root.$emit("viewDateil","no",row)
			},
			page(e){
				var that=this
				var url='/api/customer/questionnaire/record/pagingQuery?page='+e+'&pageSize=10'
				this.$http({
					url: url,
					method: 'POST',
					// 请求体重发送的数据
					headers: { 'Content-Type': 'application/json;charset=utf-8' },
					data:{}
				})
				.then(response => {
					//将数据传输到列表组件
					if(response.status==200){
						var data=response.data.info.list
						for(let i=0;i<data.length;i++){
							var num=0
							for (let j = 0; j < data[i].questionnaireRecordDetailList.length;j++){
								if (data[i].questionnaireRecordDetailList[j].correctAnswerId == data[i].questionnaireRecordDetailList[j].customerAnswerId){
									num++
								}
          					}
							data[i].num=num
						}
						that.tableData=data
						that.total=response.data.info.total
					}else{
						that.$message({
							type: 'info',
							message: '数据加载失败'
						});
					}				
				})
				.catch(error => {
					console.log(error);
				})
			}
		}
	}
</script>

<style scoped lang="less">
	@import '../subject.less';
</style>
<style type="text/css">
	.recordlist {
		margin-top: 40px;
		padding-bottom: 150px;
	}
	
	.recordlist .block {
		float: right;
		margin-top: 30px;
	}
</style>