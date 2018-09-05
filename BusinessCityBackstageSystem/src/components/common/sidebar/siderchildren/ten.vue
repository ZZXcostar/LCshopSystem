<template>
    <el-container >
      <el-aside style='overflow-x: hidden;height:700px;'>
        <el-menu default-active="subject"  router>
          <el-submenu index="1">
            <template slot="title"><i class="icon iconfont icon-datiqia dt"></i>  答题模块</template>
            <el-menu-item-group>
              <el-menu-item index="subject">答题管理</el-menu-item>
              <el-menu-item index="conclusion">结论管理</el-menu-item>
              <el-menu-item index="record">答题记录</el-menu-item>
            </el-menu-item-group>
          </el-submenu>
        </el-menu>
      </el-aside>
    </el-container>
</template>
<script>
 export default {
    data() {
      return {
       
      };
    },
    beforeMount(){
        // this.$router.push("/appointmentTime")
    },
  } 
</script>