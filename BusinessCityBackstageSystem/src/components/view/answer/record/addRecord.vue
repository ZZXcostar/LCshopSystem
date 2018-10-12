<template>
	<el-dialog id="addRec" :title="usinfo.phone" width="1200px" :visible.sync="isDial">
	    <span class="userInfo">装修称号：{{usinfo.title}}  | {{usinfo.date}}</span>	
		<el-table :data="gridData">
			<el-table-column type="index" width="50">
			</el-table-column>
			<el-table-column prop="subjectContent" width="600" label="题目">
			</el-table-column>
			<el-table-column prop="correctAnswerContent" label="正确答案" >
			</el-table-column>
			<el-table-column prop="customerAnswerContent" label="用户答案" >
			</el-table-column>
		  </el-table>
	</el-dialog>
</template>

<script>
	export default {
		data() {
			return {
				title:"",
				gridData:[{
					subject:'这是题目这是题目这是题目这是题目这是题目这是题目这是题目这是题目',
					correct:'这是正确答案',
					userAnswer:'这是用户答案'
				},{
					subject:'这是题目这是题目这是题目这是题目这是题目这是题目这是题目这是题目',
					correct:'这是正确答案',
					userAnswer:'这是用户答案'
				},{
					subject:'这是题目这是题目这是题目这是题目这是题目这是题目这是题目这是题目',
					correct:'这是正确答案',
					userAnswer:'这是用户答案'
				},
				{
					subject:'这是题目这是题目这是题目这是题目这是题目这是题目这是题目这是题目',
					correct:'这是正确答案',
					userAnswer:'这是用户答案'
				}],
				formLabelWidth: '120px',
				isDial:false,
				usinfo:{
					phone:'',
					title:'',
					date:''
				}
			}
		},
		created() {
			this.$root.$on('viewDateil', (data,row) => {
				console.log(data)
				if (data == 'no') {
						this.usinfo.phone = '用户名：'+ row.customerPhone;
						this.usinfo.title = row.resultTitle;
						this.usinfo.date = row.createTime;
						this.isDial = true;
						var aa='';
						aa=row.questionnaireRecordDetailList
						console.log(aa)
						for(let i=0;i<aa.length;i++){
							if(aa[i].correctAnswerContent.indexOf(aa[i].correctAnswerLabel+'、') != -1){
								break
							}else{
								aa[i].correctAnswerContent=aa[i].correctAnswerLabel+'、'+aa[i].correctAnswerContent
								aa[i].customerAnswerContent=aa[i].customerAnswerLabel+'、'+aa[i].customerAnswerContent
							}
						}
						console.log(aa)
						this.gridData=row.questionnaireRecordDetailList
				}
			});
		},
		methods: {
			
		}
	}
</script>

<style>
	#addRec .imgBox {
		width: 100px;
		height: 100px;
		border: 1px solid #ddd;
	}
	#addRec .el-dialog{
		margin-top: 80px !important;
		
	}
	#addRec .el-dialog .userInfo{
		position: absolute;
		top: 15px;
		right: 60px;
	}
	#addRec .lineBline {
		border-bottom: 1px solid #ddd;
	}
	
	#addRec .correct {
		border-radius: 50%;
		width: 40px;
		height: 40px;
		text-align: center;
		padding: 0px;
		margin-left: 20px;
	}
	
	#addRec .correct:hover {
		background: #27a1f2;
		color: #FFFFFF;
	}
	
</style>