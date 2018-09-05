<template>
	<div class="list">
		<el-row :gutter="20" >
		  <el-col :span="8" v-for='(item,index) in jectList' :key="index">
		  	<div class="grid-content bg-purple">
		  		<div class="jectTop">
		  			第{{index+1}}题
		  			<div class="right">
		  				<span @click="isadd(index,item.id)">编辑</span>
		  				<span @click="del(index,item.id)">删除</span>
		  				<i class="icon iconfont icon-zuojiantou" @click="forward(index,item.id)"></i>
		  				<i class="icon iconfont icon-zuojiantou-fuben" @click="backward(index,item.id)"></i>
		  			</div>
		  		</div>
		  		<div class="jectb">
		  			<p>{{item.title}}</p>
		  			<span v-for="(ite,ind) in item.questionnaireAnswerList" :key="ind">{{ite.label}}.{{ite.content}} <h3 v-if=" ite.isCorrect==true">{{ite.label}}</h3></span>
		  		</div>
		  	</div>
		  </el-col>
		</el-row>
	</div>
</template>
<script>
	export default{
		data(){
			return{
				jectList:[
					{
						timu:'拿到房子后，准备装修是下面准备装修是下面哪种做法哪种做法是正确的？',
						zqdaan:'A',
						op1:"找设计师进行设计",
						op2:"找设计师进行设计",
						op3:"找设计师进行设计",
						op4:"找设计师进行设计",
					}
				],
				ind:''
			}
		},
		created(){
			var that=this
			var url='/api/customer/questionnaire/subject/pagingQuery?page=1&pageSize=10'
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
					that.jectList=response.data.info.list
				}else{
					that.$message({
						type: 'info',
						message: '数据加载失败'
					});
				}
									
			})
			.catch(error => {
				console.log(error);
				//alert('网络错误，不能访问');
			})
			//新增题目 和修改题目
			this.$root.$on('isAdd', (data,isedit) => {
				if(isedit==false){
					console.log(data)
					// var obj={}
					this.jectList.push(data)
					// obj.title=data.title;
					// obj.questionnaireAnswerList=data.questionnaireAnswerList
					var ind=this.ind
				}else{
					this.ind="";
				}
			});
		},
		methods: {
			del(index,id) { //题目删除
				var that=this
				this.$confirm('此操作将永久删除该题目, 是否继续?', '提示', {
				confirmButtonText: '确定',
				cancelButtonText: '取消',
				type: 'warning'
				}).then(() => {
					var url='/api/customer/questionnaire/subject/delete'
					this.$http({
						url: url,
						method: 'POST',
						// 请求体重发送的数据
						headers: { 'Content-Type': 'application/json;charset=utf-8' },
						data:[id]
					})
					.then(response => {
						console.log(response)
						//将数据传输到列表组件
						if(response.status==200){
							that.$message({
								type: 'success',
								message: '删除成功!'
							});
							that.jectList.splice(index, 1)
						}else{
							that.$message({
								type: 'info',
								message: '删除失败！'
							});
						}				
					})
					.catch(error => {
						console.log(error);
						//alert('网络错误，不能访问');
					})
				}).catch(() => {
				this.$message({
					type: 'info',
					message: '已取消删除'
				});         
	        });
	      },
			isadd(index,id){ //题目编辑
				console.log(index)
				this.ind=index;
				console.log(this.ind)
				let ject=this.jectList[index]
				this.$root.$emit("isadd", 'edit',ject,id)
			},
			forward(index,id){ //向前一个
				if(index==0){
					this.$message({
						type: 'info',
						message: '已经是第一个了，不能再往前了'
					});
				}else{
					var list=this.jectList
					let next=list[index-1].sortNumber  //上一个sort
					let sort=list[index].sortNumber    //当前的sort
					let nextid=list[index+1].id        //上一个id
					this.setjectr(id,next)
					this.setjectr(nextid,sort)
					var newlist="";
					newlist=this.jectList[index];
					this.jectList.splice(index,1,this.jectList[index-1]);
					this.jectList.splice(index-1,1,newlist);
				}
			},
			backward(index,id){ //向后一个
				var len=this.jectList.length
				if(index==len-1){
					this.$message({
						type: 'info',
						message: '已经是最后一个了，不能再往后了'
					}); 
				}else{
					var list=this.jectList
					let next=list[index+1].sortNumber  //下一个sort
					let sort=list[index].sortNumber    //当前的sort
					let nextid=list[index+1].id        //下一个id
					this.setjectr(id,next)
					this.setjectr(nextid,sort)
					var newlist="";
					newlist=this.jectList[index];
					this.jectList.splice(index,1,this.jectList[index+1]);
					this.jectList.splice(index+1,1,newlist);
				}
			},
			//单条数据请求
			setjectr(id,sort){
				var that=this
				var url='/api/customer/questionnaire/subject/update'
				this.$http({
					url: url,
					method: 'POST',
					// 请求体重发送的数据
					headers: { 'Content-Type': 'application/json;charset=utf-8' },
					data:{
						id:id,
						sortNumber:sort
					}
				})
				.then(response => {
					console.log(response)
					// 将数据传输到列表组件
					if(response.status==200){
						that.$message({
								type: 'success',
								message: '交换成功!'
						});
					}
										
				})
				.catch(error => {
					console.log(error);
					//alert('网络错误，不能访问');
				})
			}
	    }
	}
</script>

<style>
.list {
	margin-top: 30px;
	width: 100%;
	padding-bottom: 50px;
}

.list .el-row {
	margin-bottom: 20px;
}

.list .el-col {
	border-radius: 4px;
	margin-top: 10px;
	font-size: 16px;
	color: #666666;
	overflow: hidden;
}

.list .bg-purple {
	border: 1px solid #d9d9d9;
	padding: 10px 18px;
}

.list .grid-content {
	border-radius: 4px;
	min-height: 36px;
}

.list .row-bg {
	padding: 10px 0;
	background-color: #f9fafc;
}

.jectb p{
	height: 64px !important;
}
</style>
<style scoped lang="less">
	@import './subjectList.less';
</style>