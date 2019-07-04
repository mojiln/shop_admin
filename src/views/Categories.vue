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
      <el-table-column prop="cat_name" label="分类名称" width="180"></el-table-column>
      <el-table-column prop="name" label="是否有效" width="180"></el-table-column>
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
export default {
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
    },
    // 分页页面改变
    onPageChange(page) {
      this.currentPage = page;
      this.getCategoryList();
    }
  }
};
</script>