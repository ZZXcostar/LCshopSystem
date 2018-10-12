<template>
	<el-dialog id="addCon" :title="title" width="494px" :visible.sync="isDial">
		<el-form :model="form">
			<el-form-item label="装修称号：" :label-width="formLabelWidth">
				<el-input v-model="form.name" placeholder="请输入问题" auto-complete="off" class="contimu"></el-input>
				<p ref="pp">*文字要求4-5字之间</p>
			</el-form-item>
			<el-form-item label="答对区间：" :label-width="formLabelWidth">
				<el-select v-model="form.min" placeholder="0" width="81px" class="select">
					<el-option :label="item.num" :value="item.num" v-for="(item,index) in num" :key="index"></el-option>
				</el-select>
				<span>最少</span>
				<el-select v-model="form.max" placeholder="10" width="81px" class="select">
					<el-option :label="item.num" :value="item.num" v-for="(item,index) in num" :key="index"></el-option>
				</el-select>
				<span>最多</span>
				<p ref="qq">*答题区间不能重复</p>
			</el-form-item>
			<el-form-item label="结论：" :label-width="formLabelWidth">
				<el-input type="textarea" :rows="4" placeholder="请输入内容" class='textare' v-model='form.textare'>
				</el-input>
			</el-form-item>
		</el-form>
		<div slot="footer" class="dialog-footer">
			<el-button @click="isAdd('cancel')">取 消</el-button>
			<el-button type="primary" @click="isAdd('sure')">确 定</el-button>
		</div>
	</el-dialog>
</template>
<script>
export default {
  data() {
    return {
      title: "",
      form: {
        name: "",
        textare: "",
        min: "",
        max: ""
      },
      num: [
        { num: 0 },
        { num: 1 },
        { num: 2 },
        { num: 3 },
        { num: 4 },
        { num: 5 },
        { num: 6 },
        { num: 7 },
        { num: 8 },
        { num: 9 },
        { num: 10 }
      ],
      formLabelWidth: "120px",
      isDial: false,
      edit: false,
      id: "",
      isSection: false
    };
  },
  created: function() {
    this.$root.$on("addConclusion", (data, con, id) => {
      if (data != "no") {
        this.title = "编辑称号";
        this.isDial = true;
        this.edit = true;
        let text = "";
        for (let i = 0; i < con.content.length; i++) {
          text += con.content[i] + " ";
        }
        this.form.name = con.title;
        this.form.min = con.minCorrect;
        this.form.max = con.maxCorrect;
        this.form.textare = text;
        this.id = id;
        console.log(this.id);
      } else {
        this.title = "新增称号";
        this.isDial = true;
        this.edit = false;
        this.form.textare = "";
        this.form.name = "";
        this.form.min = 0;
        this.form.max = 10;
      }
    });
  },
  //   mounted(){
  // 	this.$refs.pp.style.color = "#606266";
  //     this.$refs.qq.style.color = "#606266";
  //   },
  methods: {
    //判断区间和称号是否重复
    section(aa) {
      var that = this;
      var url = "/api/customer/questionnaire/result/pagingQuery";
      if (aa == "sure") {
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
              console.log(response.data.info.list);
              var list = response.data.info.list;
              var arr = [];
              arr.splice(0, arr.length);
              for (let i = 0; i < list.length; i++) {
                if (list[i].title == that.form.name && that.edit == false) {
                  that.$refs.pp.innerHTML = "*称号不能重复";
                  that.$refs.pp.style.color = "red";
                  return;
                } else {
                  that.$refs.pp.style.color = "#606266";
                  that.$refs.qq.style.color = "#606266";
                }
                for (let j = list[i].minCorrect; j <= list[i].maxCorrect; j++) {
                  if (that.edit == false) {
                    arr.push(j);
                  } else {
                    if (list[i].id != that.id) {
                      arr.push(j);
                    }
                  }
                }
              }
              console.log(that.id);
              console.log(arr);
              arr.sort(function(a, b) {
                return a - b;
              });
              for (let z = 0; z < arr.length; z++) {
                for (let x = that.form.min; x <= that.form.max; x++) {
                  if (arr[z] == x) {
                    that.$refs.qq.innerHTML = "*区间不能重叠";
                    that.$refs.qq.style.color = "red";
                    return;
                  } else {
                    that.$refs.pp.style.color = "#606266";
                    that.$refs.qq.style.color = "#606266";
                  }
                }
              }

              if (
                that.$refs.pp.style.color != "red" &&
                that.form.name.length >= 4 &&
                that.form.name.length <= 5
              ) {
                if (that.form.textare != "") {
                  if (that.$refs.qq.style.color != "red") {
                    var form = that.form;
                    if (that.edit) {
                      var data = JSON.stringify({
                        id: that.id,
                        title: that.form.name,
                        content: that.form.textare,
                        minCorrect: that.form.min,
                        maxCorrect: that.form.max
                      });
                      var url = "/api/customer/questionnaire/result/update";
                      that.setData(url, data);
                    } else {
                      var data = JSON.stringify([
                        {
                          title: that.form.name,
                          content: that.form.textare,
                          minCorrect: that.form.min,
                          maxCorrect: that.form.max
                        }
                      ]);
                      var url = "/api/customer/questionnaire/result/insert";
                      that.setData(url, data);
                    }
                    let form = that.form;
                    console.log(form);
                    that.$root.$emit("isAdd", form, that.edit);
                    that.edit = false;
                    that.isDial = false;
                    // that.id = "";
                    that.$refs.pp.style.color = "#606266";
                    that.$refs.qq.style.color = "#606266";
                  } else {
                    that.$refs.pp.style.color = "#606266";
                    that.$refs.qq.style.color = "#606266";
                    return;
                  }
                } else {
                  that.$message({
                    type: "success",
                    message: "称号描述不能为空！"
                  });
                }
              } else {
                that.$refs.pp.style.color = "#606266";
                that.$refs.qq.style.color = "#606266";
                return;
              }
            } else {
              that.$message({
                type: "success",
                message: response.msg
              });
            }
          })
          .catch(error => {
            console.log(error);
          });
      } else {
        that.isDial = false;
      }
    },
    //确定按钮点击事件
    isAdd(aa) {
      this.section(aa);
    },
    // 设置  提交数据 事件
    setData(url, data) {
      var that = this;
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
            that.isDial = false;
            that.edit = false;
            console.log(response);
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
#addCon .imgBox {
  width: 100px;
  height: 100px;
  border: 1px solid #ddd;
}

#addCon .lineBline {
  border-bottom: 1px solid #ddd;
}

#addCon .on {
  color: red !important;
}

#addCon .memberdialog .el-dialog {
  height: 60%;
}

#addCon .option {
  width: 60%;
}

#addCon .contimu {
  width: 267px;
}

#addCon .correct {
  border-radius: 50%;
  width: 40px;
  height: 40px;
  text-align: center;
  padding: 0px;
  margin-left: 20px;
}

#addCon .correct:hover {
  background: #27a1f2;
  color: #ffffff;
}

#addCon .el-form-item .span {
  margin-left: 45px;
}

#addCon .select {
  width: 81px !important;
  margin-right: 8px;
}
#addCon .textare {
  width: 276px;
  text-align: center;
  resize: none !important;
}
</style>