<template>
	<el-dialog :title="title" :visible.sync="isDial">
	  <el-form :model="form">
	    <el-form-item label="问题：" :label-width="formLabelWidth">
	      <el-input v-model="form.title" placeholder="请输入问题" auto-complete="off" class="timu"></el-input>
	      <p>*文字要求30字以内</p>
	    </el-form-item>
	    <el-form-item label="选项：" :label-width="formLabelWidth" v-for="(item,index) in form.questionnaireAnswerList" :key="index">
	      <el-input v-model="item.content" placeholder="请输入选项" auto-complete="off" class="option"></el-input>
	      <span class="span">正确答案</span><el-button circle :class="index == actNum ? 'correct on' :'correct'" @click="correct(index)">{{item.label}}</el-button>
	    </el-form-item>
	  </el-form>
	  <div slot="footer" class="dialog-footer">
	    <el-button @click="isAdd('cancel')">取 消</el-button>
	    <el-button type="primary" @click="isAdd('sure')">确 定</el-button>
	  </div>
	</el-dialog>
</template>

<script>
import qs from "qs";
export default {
  data() {
    return {
      isDial: false,
      title: "新增题目",
      form: {},
      subjectId: "",
      edit: false,
      actNum: 0,
      formLabelWidth: "120px",
    };
  },
  created: function() {
    this.$root.$on("isadd", (data, ject, id) => {
      if (data != "no") {
        this.form = ject;
        this.subjectId = id;
        this.title = "编辑题目";
        for (var i = 0; i < ject.questionnaireAnswerList.length; i++) {
          if (ject.questionnaireAnswerList[i].isCorrect == true) {
            this.actNum = i;
          }
        }
        this.isDial = true;
        this.edit = true;
        console.log(id)
        console.log(ject)
      } else {
        this.title = "新增题目";
        this.isDial = true;
        this.form.title = "";
        this.form.questionnaireAnswerList = [
          { label: "A",isCorrect:true},
          { label: "B" ,isCorrect:false},
          { label: "C" ,isCorrect:false},
          { label: "D" ,isCorrect:false}
        ];
        this.actNum = 0;
        this.edit = false;
      }
    });
  },
  methods: {
    isAdd(aa) {
      var that = this;
      var from = this.form;
      var list = this.form.questionnaireAnswerList;
      var act = 0;
      for (let i = 0; i < list.length; i++) {
        if (list[i].content == "") {
          act++;
          this.$message({
            type: "success",
            message: "选项不能为空！"
          });
        }
      }
      //输入非空判断
      if (aa == "sure") {
        if (from.title != "") {
          if (act == 0) {
            if (that.edit==true) {
							// 题目更新
              let url = "/api/customer/questionnaire/subject/update";
              that.setData(url);
            } else {
							// 添加题目
              let url = "/api/customer/questionnaire/subject/insert";
              that.setData(url);
            }
          }
        } else {
          this.$message({
            type: "success",
            message: "题目不能为空！"
          });
        }
      } else {
        this.isDial = false;
      }
    },
    correct(index) {
      this.actNum = index;
      var list = this.form.questionnaireAnswerList;
      for (let i = 0; i < list.length; i++) {
        if (i == index) {
          list[i].isCorrect = true;
        } else {
          list[i].isCorrect = false;
        }
      }
		},
    setData(url) {
			var that=this
			var list = this.form.questionnaireAnswerList;
			if(this.edit==false){
				var data = JSON.stringify([
					{
						label: "第1题",
						title: this.form.title,
						questionnaireAnswerList:[
            {
              label: "A",
              content: list[0].content,
              sortNumber: 1,
              isCorrect: list[0].isCorrect
            },
            {
              label: "B",
              content: list[1].content,
              sortNumber: 2,
              isCorrect: list[1].isCorrect
            },
            {
              label: "C",
              content: list[2].content,
              sortNumber: 3,
              isCorrect: list[2].isCorrect
            },
            {
              label: "D",
              content: list[3].content,
              sortNumber: 4,
              isCorrect: list[3].isCorrect
            }
          ]
					}
				]);
			}else{
				var data = JSON.stringify(
					{
						id:this.form.id,
						label:'第一题',
						title: this.form.title,
						questionnaireAnswerList :[
            {
							id:list[0].id,
              label: "A",
              content: list[0].content,
              isCorrect: list[0].isCorrect
            },
            {
							id:list[1].id,
              label: "B",
              content: list[1].content,
              isCorrect: list[1].isCorrect
            },
            {
							id:list[2].id,
              label: "C",
              content: list[2].content,
              isCorrect: list[2].isCorrect
            },
            {
							id:list[3].id,
              label: "D",
              content: list[3].content,
              isCorrect: list[3].isCorrect
            }
          ]
					}
				);
			}
      this.$http({
        url: url,
        method: "POST",
        // 请求体重发送的数据
        headers: { "Content-Type": "application/json;charset=utf-8" },
        data: data
      })
        .then(response => {
					//将数据传输到列表组件
          if (response.status == 200) {
						console.log(that.isDial)
						if(that.edit == false){
							let form = response.data.info[0];
            	that.$root.$emit("isAdd", form, that.edit);
						}else{
							that.$root.$emit("isAdd", 'form', that.edit);
            }
            that.isDial = false;
						that.edit = false;
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
    }
  }
};
</script>

<style>
.imgBox {
  width: 100px;
  height: 100px;
  border: 1px solid #ddd;
}
.lineBline {
  border-bottom: 1px solid #ddd;
}
.memberdialog .el-dialog {
  height: 60%;
}
.option {
  width: 60%;
}
.timu {
  width: 90%;
}
.correct {
  border-radius: 50%;
  width: 40px;
  height: 40px;
  text-align: center;
  padding: 0px;
  margin-left: 20px;
}
.correct:hover {
  background: #27a1f2;
  color: #ffffff;
}
.correct.on {
  background: #27a1f2 !important;
  color: #ffffff !important;
}
.el-form-item .span {
  margin-left: 45px;
}
</style>