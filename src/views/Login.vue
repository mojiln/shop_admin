<template>
  <el-row type="flex" class="row-bg" justify="center" align="middle">
    <el-col :xs="14" :sm="12" :md="10" :lg="8" :xl="6">
      <el-form
        ref="loginForm"
        :model="form"
        :rules="formRules"
        class="login-form"
        label-width="80px"
        label-position="top"
      >
        <el-form-item label="用户名" prop="username">
          <el-input v-model="form.username"></el-input>
        </el-form-item>
        <el-form-item label="密码" prop="password">
          <el-input v-model="form.password" type="password"></el-input>
        </el-form-item>

        <el-form-item>
          <el-button type="primary" @click="submitForm('loginForm')">登录</el-button>
          <el-button @click="resetForm('loginForm')">重置</el-button>
        </el-form-item>
      </el-form>
    </el-col>
  </el-row>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      form: {
        username: "",
        password: ""
      },
      formRules: {
        username: [
          {
            required: true,
            message: "请输入用户名",
            trigger: "blur"
          },
          {
            min: 5,
            max: 12,
            message: "用户名必须是5个到12个字符",
            trigger: "change"
          }
        ],
        password: [
          {
            required: true,
            message: "请输入密码",
            trigger: "blur"
          },
          {
            min: 6,
            max: 12,
            message: "密码必须是6个到15个字符",
            trigger: "change"
          }
        ]
      }
    };
  },
  methods: {
    async submitForm(formName) {
      let valid = await this.$refs[formName].validate();
      if (valid) {
        try {
          let res = await axios({
            url: "http://localhost:8888/api/private/v1/login",
            method: "post",
            data: this.form
          });
          // .then(({ data: { data, meta } }) => {
          //   // console.log(res);
          //   if (meta.status === 200) {
          //     // 登录成功之后，服务器端会返回给我们一个token
          //     // 我们需要将这个token保存到本地
          //     // 保存到localstorage中就可以
          //     localStorage.setItem("token", data.token);
          //     this.$router.push("/home");

          if (res.data.meta.status === 200) {
            localStorage.setItem("token", res.data.data.token);
            this.$router.push("/home");
          } else {
            // alert("用户密码输入错误");
            // return false;
            this.$message({
              message: res.data.meta.msg,
              type: "error",
              duration: 1000
            });
          }
        } catch (e) {
          console.log(e);
        }
      }
    },
    resetForm(formName) {
      this.$refs[formName].resetFields();
    }
  }
};
</script>


<style>
.row-bg {
  height: 100%;
  background-color: #2d434c;
}
.login-form {
  background-color: #fff;
  border-radius: 10px;
  padding: 30px 20px;
  min-width: 375px;
}
</style>
