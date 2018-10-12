<template>
	<div class="conList">
		<el-card class="box-card" v-for="(item,index) in conList" :key="index">
		  <div slot="header" class="clearfix">
		    <span>{{item.title}}</span>
		    <img src="./icon.png"/>
		  </div>
		  <div class="clearB">
		  	<p v-for="(ite,ind) in item.content" :key="ind">{{ite}}</p>
		  </div>
		  <div class="operation">
		  	<span>答对区间：{{item.minCorrect}}-{{item.maxCorrect}}</span>
		  	<div class="right">
		  		<span @click='addConclusion(index,item.id)'>编辑</span>
		  		<span @click="del(index,item.id)">删除</span>
		  	</div>
		  </div>
		</el-card>
	</div>
</template>

<script>
export default {
  data() {
    return {
      namepage: "结论管理",
      ind: "",
      conList: [
        {
          title: "装修小渣渣",
          text: [
            "难以置信，",
            "阁下功力精纯深厚",
            "佩服佩服！",
            "下方内功心法或可一参！"
          ],
          min: 0,
          max: 3
        },
      ]
    };
  },
  created() {
    var that = this;
    var url = "/api/customer/questionnaire/result/pagingQuery";
    this.$http({
      url: url,
      method: "POST",
      // 请求体重发送的数据
      headers: { "Content-Type": "application/json;charset=utf-8" },
      data: {}
    })
      .then(response => {
				//将数据传输到列表组件
        if (response.status == 200) {
          var data = response.data.info.list;
          for (let i = 0; i < data.length; i++) {
            data[i].content = data[i].content.split(/\s+/); //字符按换行符切割成数组
          }
          that.conList = data;
        } else {
          that.$message({
            type: "success",
            message: response.msg
          });
        }
      })
      .catch(error => {
        console.log(error);
        //alert('网络错误，不能访问');
      });
			this.$root.$on('isAdd', (data,isedit) => {
				let ind=this.ind
				var obj={};
				obj.title=data.name;
				obj.minCorrect=data.min;
				obj.maxCorrect=data.max;
				var text=data.textare.split(/\s+/) //字符按换行符切割成数组
				obj.content=text
				if(isedit===false){
					this.conList.push(obj)
				}else{
					this.conList.splice(ind,1,obj)
					this.ind="";
				}
			});
  },
  methods: {
    del(index, id) {
      //题目删除
      var that = this;
      this.$confirm("此操作将永久删除该称号, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      })
        .then(() => {
          var url = "/api/customer/questionnaire/result/delete";
          this.$http({
            url: url,
            method: "POST",
            // 请求体重发送的数据
            headers: { "Content-Type": "application/json;charset=utf-8" },
            data: [id]
          })
            .then(response => {
              //将数据传输到列表组件
              if (response.status == 200) {
                this.$message({
                  type: "success",
                  message: "删除成功!"
                });
                that.conList.splice(index, 1);
              } else {
                that.$message({
                  type: "success",
                  message: response.msg
                });
              }
            })
            .catch(error => {
              console.log(error);
              //alert('网络错误，不能访问');
            });
        })
        .catch(() => {
          this.$message({
            type: "info",
            message: "已取消删除"
          });
        });
    },
    addConclusion(index,id) {
      //题目编辑
      var con = this.conList[index];
      this.ind = index;
      this.$root.$emit("addConclusion", "edit", con,id);
    },
    listId() {
      this.$root.$emit("listId", "no");
    }
  }
};
</script>
<style scoped lang="less">
@import "../subject.less";
</style>
<style type="text/css">
.conList .clearfix:before,
.conList .clearfix:after {
  display: table;
  content: "";
}
.conList {
  margin-top: 50px;
}
.conList .box-card {
  padding: 0px;
  float: left;
  margin-right: 20px;
  margin-bottom: 20px;
}
.conList .clearB {
  width: 100%;
  height: 100px;
}
.conList .clearB p {
  text-align: center;
  color: #ffffff;
  line-height: 27px;
}
.conList .clearfix {
  height: 105px;
  background: #3daaf3;
  text-align: center;
  line-height: 50px;
  color: #ffffff;
  font-size: 24px;
  padding: 0px;
  overflow: hidden;
}
.conList .operation {
  width: 100%;
  margin-top: 63px;
  color: #ffffff;
}
.conList .operation .right {
  float: right;
}
.conList .operation .right span {
  padding: 0px 10px;
  cursor: pointer;
}
.conList .operation .right span:nth-child(1) {
  border-right: 1px solid #ffffff;
}
.conList .clearfix:after {
  clear: both;
}
.conList .clearfix img {
  display: block;
  margin: 0 auto;
}
.conList .box-card {
  width: 340px;
  background: #3caaf3;
}
.conList .conList {
  margin-top: 40px;
  padding-bottom: 100px;
  overflow: hidden;
}
</style>