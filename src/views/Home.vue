<template>
  <el-container>
    <el-header>
      <el-row type="flex" class="row-bg" justify="space-between" align="middle">
        <el-col :span="6">
          <img src="../assets/header_logo.png" width="200px" />
        </el-col>
        <el-col class="title">
          <div>自由买后台管理系统</div>
        </el-col>
        <el-col :span="6">
          <div class="login">
            欢迎星耀会员墨迹
            <a @click="logout">退出</a>
          </div>
        </el-col>
      </el-row>
    </el-header>
    <el-container>
      <el-aside width="200px">
        <el-menu
          :default-active="$route.path"
          class="el-menu-vertical-demo"
          background-color="#545c64"
          text-color="#fff"
          active-text-color="#ffd04b"
          :unique-opened="true"
          :router="true"
        >
          <el-submenu :index="item.id + ''" v-for="item in menusList" :key="item.id">
            <template slot="title">
              <i class="el-icon-location"></i>
              <span>{{item.authName}}</span>
            </template>
            <el-menu-item v-for="item1 in item.children" :key="item1.id" :index=" '/' + item1.path">
              <i class="el-icon-menu"></i>
              <span>{{item1.authName}}</span>
            </el-menu-item>
          </el-submenu>
        </el-menu>
      </el-aside>
      <el-main>
        <router-view></router-view>
      </el-main>
    </el-container>
  </el-container>
</template>

<script>
export default {
  data() {
    return {
      menusList: []
    };
  },
  methods: {
    // 退出功能
    async logout() {
      let confim = await this.$confirm("您是否确定退出?", "温馨提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      });
      // 退出登录逻辑：
      if (confim) {
        this.$router.push("/login");
        localStorage.removeItem("token");
      } else {
        this.$message({
          type: "info",
          message: "已取消退出"
        });
      }
    }
  },
  // 左侧菜单权限
  async created() {
    let res = await this.$http({
      url: "menus"
    });
    // console.log(res);
    this.menusList = res.data.data;
  }
};
</script>


<style>
.el-container {
  height: 100%;
}
.el-container .el-header {
  padding: 0;
  margin: 0;
}
.title {
  text-align: center;
  color: #fff;
  font-size: 28px;
  font-weight: 700;
}
.login {
  color: #fff;
}
.login a {
  color: chocolate;
  text-decoration: none;
  font-weight: bold;
}
.el-menu-vertical-demo.el-menu {
  height: 100%;
}
</style>
