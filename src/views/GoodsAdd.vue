<template>
  <div>
    <!-- 面包屑 -->
    <el-breadcrumb separator-class="el-icon-arrow-right" class="breadcrumb">
      <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>商品管理</el-breadcrumb-item>
      <el-breadcrumb-item>添加商品</el-breadcrumb-item>
    </el-breadcrumb>
    <!-- 步骤条 -->
    <el-steps :active="activeStep" finish-status="success">
      <el-step title="第一步" description="基本信息"></el-step>
      <el-step title="第二步" description="商品图片"></el-step>
      <el-step title="第三步" description="商品内容"></el-step>
    </el-steps>
    <!-- tabs标签页 -->
    <!-- v-model="activeName" v-model属性用来绑定 当前正在激活的标签的name -->
    <!-- value可以实现，标签切换 -->
    <el-tabs tab-position="left" @tab-click="changeTab" v-model="activeName">
      <el-tab-pane label="基本信息" name="basic">
        <!-- 表单 -->
        <el-form ref="form" :model="goodForm" label-width="80px">
          <el-form-item label="商品名称">
            <el-input v-model="goodForm.goods_name"></el-input>
          </el-form-item>
          <el-form-item label="商品价格">
            <el-input v-model="goodForm.goods_price"></el-input>
          </el-form-item>
          <el-form-item label="商品重量">
            <el-input v-model="goodForm.goods_weight"></el-input>
          </el-form-item>
          <el-form-item label="商品数量">
            <el-input v-model="goodForm.goods_number"></el-input>
          </el-form-item>
          <el-form-item label="商品分类">
            <el-cascader v-model="goodForm.catArr" :options="cateOptions" :props="defaultProps"></el-cascader>
          </el-form-item>
          <el-form-item label="是否促销">
            <el-radio-group v-model="goodForm.is_promote">
              <el-radio label="1">是</el-radio>
              <el-radio label="0">否</el-radio>
            </el-radio-group>
          </el-form-item>
        </el-form>
        <!-- 下一步 -->
        <el-button @click="next(1,'pic')" type="primary">下一步</el-button>
      </el-tab-pane>
      <el-tab-pane label="商品图片" name="pic">
        <!-- action 图片上传的地址 -->
        <!-- action中必须要写完整的url， 因为之前设置的baseURL和这个没关系，那个只是给axios -->
        <el-upload
          action="http://localhost:8888/api/private/v1/upload"
          list-type="picture-card"
          :headers="headers"
          :on-success="onFileUploadSuccess"
        >
          <el-button size="small" type="primary">点击上传</el-button>
          <div slot="tip" class="el-upload__tip">只能上传jpg/png文件，且不超过500kb</div>
        </el-upload>
        <el-button @click="next(2,'content')" type="primary" style="margin-top:10px">下一步</el-button>
      </el-tab-pane>
      <el-tab-pane label="商品内容" name="content">
        <quill-editor v-model="goodForm.goods_introduce"></quill-editor>
        <el-button type="primary" @click="addGoods" style="margin-top:10px">确定</el-button>
      </el-tab-pane>
    </el-tabs>
  </div>
</template>
<script>
import "quill/dist/quill.core.css";
import "quill/dist/quill.snow.css";
import "quill/dist/quill.bubble.css";
import { quillEditor } from "vue-quill-editor";
export default {
  components: {
    quillEditor
  },
  data() {
    return {
      headers: {
        Authorization: localStorage.getItem("token")
      },
      activeStep: 0,
      activeName: "basic",
      goodForm: {
        goods_name: "",
        goods_price: 0,
        goods_number: 0,
        goods_weight: 0,
        // 用级联列表，用数组存起来
        catArr: [],
        goods_introduce: "",
        pics: [],
        is_promote: ""
      },
      cateOptions: [],
      defaultProps: {
        value: "cat_id",
        label: "cat_name"
      }
    };
  },
  async created() {
    let res = await this.$http({
      url: "categories",
      params: {
        type: 3
      }
    });
    // console.log(res);
    this.cateOptions = res.data.data;
  },
  methods: {
    next(index, activeName) {
      // 1. 把步骤条进行切换
      this.activeStep = index;
      // 2. 把tabs进行切换
      this.activeName = activeName;
    },
    changeTab(tab) {
      // console.log(tab);
      this.activeStep = +tab.index;
    },
    onFileUploadSuccess(res) {
      // console.log(res);
      this.goodForm.pics.push({
        pic: res.data.tmp_path
      });
    },
    async addGoods() {
      // console.log(this.goodForm);
      let newGoods = {
        goods_name: this.goodForm.goods_name,
        goods_price: this.goodForm.goods_price,
        goods_number: this.goodForm.goods_number,
        goods_weight: this.goodForm.goods_weight,
        goods_cat: this.goodForm.catArr.join(),
        goods_introduce: this.goodForm.goods_introduce,
        pics: this.goodForm.pics,
        is_promote: this.goodForm.is_promote
      };
      let res = await this.$http({
        url: "goods",
        method: "post",
        data: newGoods
      });
      // console.log(res);
      if (res.data.meta.status === 201) {
        this.$router.push("/goods");
      }
    }
  }
};
</script>
<style>
.el-steps.el-steps--horizontal {
  margin-bottom: 15px;
}
</style>

