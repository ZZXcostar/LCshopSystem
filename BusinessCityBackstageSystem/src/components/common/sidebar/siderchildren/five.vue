<template>
    <el-container >
      <el-aside style='overflow-x: hidden;height:700px;'>
        <el-menu :default-openeds="['1']">
          <el-submenu index="1">
            <template slot="title"><i class="el-icon-message"></i>答题模块</template>
            <el-menu-item-group>
              <el-menu-item index="1-1">答题管理</el-menu-item>
              <el-menu-item index="1-2">结论管理</el-menu-item>
              <el-menu-item index="1-2">答题记录</el-menu-item>
            </el-menu-item-group>
          </el-submenu>
        </el-menu>
      </el-aside>
    </el-container>
</template>