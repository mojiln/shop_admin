<template>
  <div>
    <!-- 面包屑 -->
    <el-breadcrumb separator-class="el-icon-arrow-right" class="breadcrumb">
      <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>商品管理</el-breadcrumb-item>
      <el-breadcrumb-item>商品列表</el-breadcrumb-item>
    </el-breadcrumb>
    <!-- 添加按钮 -->
    <el-button type="success" plain @click="$router.push('/goodsAdd')">添加商品</el-button>
    <!-- 表格 -->
    <el-table :data="goodsList" stripe style="width: 100%">
      <el-table-column type="index" :index="indexMethod" width="50"></el-table-column>
      <el-table-column prop="goods_name" label="商品名称" width="180"></el-table-column>
      <el-table-column prop="goods_price" label="商品价格" width="180"></el-table-column>
      <el-table-column prop="goods_weight" label="商品重量"></el-table-column>
      <el-table-column prop="add_time" label="创建时间"></el-table-column>
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
      goodsList: [],
      pagenum: 1,
      pagesize: 6,
      total: 0
    };
  },
  methods: {
    async getGoodsList() {
      let res = await this.$http({
        url: "goods",
        params: {
          pagenum: this.pagenum,
          pagesize: this.pagesize
        }
      });
      // console.log(res);
      this.goodsList = res.data.data.goods;
      this.total = res.data.data.total;
    },
    // 分页页面改变
    onPageChange(page) {
      this.pagenum = page;
      this.getGoodsList();
    },
    indexMethod(index) {
      if (this.pagenum === 1) {
        return index + 1;
      } else {
        return index + 1 + (this.pagenum - 1) * this.pagesize;
      }
    }
  },
  created() {
    this.getGoodsList();
  }
};
</script>

<style>
.breadcrumb {
  margin-bottom: 10px;
}
</style>

