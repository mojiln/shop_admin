<template>
  <div>
    <!-- 面包屑 -->
    <el-breadcrumb separator-class="el-icon-arrow-right" class="breadcrumb">
      <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>商品管理</el-breadcrumb-item>
      <el-breadcrumb-item>商品分类</el-breadcrumb-item>
    </el-breadcrumb>
    <!-- 添加按钮 -->
    <el-button type="success" plain>添加分类</el-button>
    <!-- 表格 -->
    <el-table :data="categoryList" stripe style="width: 100%">
      <!-- file-icon指的就是叶子节点的图标
            folder-icon指的就是可展开的项的图标
            prop 指的就是当前列要显示的数据的属性名称
            label 指的就是表头 
            treeKey 节点的唯一表示
            parentKey 数据的父节点id
            childKey 当前节点的所有子节点存放的属性的名字 默认是children

            indent-size是用来设置每个层级之间的缩进距离的
            设置indent-size 需要同时制定 层级属性

            level-key:    设置为数据中标识层级的属性名即可

            驼峰命名法全改为 -
      -->
      <el-table-tree-column
        prop="cat_name"
        label="分类名称"
        file-icon="el-icon-notebook-2"
        folder-icon="el-icon-folder"
        width="220"
        treeKey="cat_id"
        parentKey="cat_pid"
        :indent-size="50"
        level-key="cat_level"
      ></el-table-tree-column>
      <el-table-column prop="name" label="是否有效" width="180">
        <template v-slot="{row}">{{row.cat_deleted?"否":"是"}}</template>
      </el-table-column>
      <el-table-column prop="cat_level" label="层级"></el-table-column>
      <el-table-column label="操作">
        <el-button type="primary" plain icon="el-icon-edit" size="mini"></el-button>
        <el-button type="danger" plain icon="el-icon-delete" size="mini"></el-button>
      </el-table-column>
    </el-table>
    <!-- 分页部分 -->
    <el-pagination
      background
      layout="prev, pager, next"
      :total="total"
      :page-size="pagesize"
      @current-change="onPageChange"
    ></el-pagination>
  </div>
</template>
<script>
// 当前页面中要用到 一个第三方的表格插件（ElementUI的插件）
// element-tree-grid
// 安装  npm i element-tree-grid -S
// 使用：
// 如果需要全局使用，可能很多页面里面都会用到这个组件，那么就把下面这两句代码放到main.js中
// const ElTreeGrid = require('element-tree-grid');
// Vue.component(ElTreeGrid.name, ElTreeGrid)
// 如果只是需要某个页面中使用，只需要进行局部注册组件即可
// 在使用到这个组件的页面中，引入该组件，然后进行 components 局部注册即可
const ElTreeGrid = require("element-tree-grid");
// console.log(ElTreeGrid.name);
// el-table-tree-column
// 页面中使用该组件，只需要用  el-table-tree-column 标签即可
export default {
  components: {
    [ElTreeGrid.name]: ElTreeGrid
  },
  data() {
    return {
      categoryList: [],
      total: 0,
      pagesize: 5,
      pagenum: 1
    };
  },
  created() {
    this.getCategoryList();
  },
  methods: {
    async getCategoryList() {
      let res = await this.$http({
        url: "categories",
        params: {
          type: 3,
          pagenum: this.pagenum,
          pagesize: this.pagesize
        }
      });
      console.log(res);
      this.categoryList = res.data.data.result;
      this.total = res.data.data.total;
    },
    // 分页页面改变
    onPageChange(page) {
      this.pagenum = page;
      this.getCategoryList();
    }
  }
};
</script>