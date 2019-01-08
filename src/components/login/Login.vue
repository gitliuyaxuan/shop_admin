<template>
  <el-form :model="loginForm" :rules="rules" ref="loginForm" label-width="100px" class="demo-loginForm" label-position="top">
  <el-form-item label="用户名" prop="username">
    <el-input v-model="loginForm.username"></el-input>
  </el-form-item>
  <el-form-item label="密码" prop="password">
    <el-input v-model="loginForm.password"></el-input>
  </el-form-item>

  <el-form-item>
    <el-button type="primary" @click="submitForm('loginForm')">登录</el-button>
    <el-button @click="resetForm('loginForm')">重置</el-button>
  </el-form-item>
</el-form>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      loginForm: {
        // 用户名
        username: "",
        // 密码
        password: ""
      },
      rules: {
        username: [
          { required: true, message: "请输入用户名", trigger: "blur" },
          {
            min: 5,
            max: 12,
            message: "用户名长度为5 - 12个字符",
            trigger: "blur"
          }
        ],
        password: [
          { required: true, message: "请输入密码", trigger: "blur" },
          {
            min: 6,
            max: 12,
            message: "密码长度为6 - 12个字符",
            trigger: "blur"
          }
        ]
      }
    };
  },
  methods: {
    // $refs 是Vue中提供的一个对象 作用: 用来获取页面中所有带有ref属性的元素 (DOM或组件) 因为Vue在有些情况需要手动操作DOM 此时 可以通过ref来获取DOM对象 然后再进行DOM操作
    submitForm(formName) {
      this.$refs[formName].validate(valid => {
        if (!valid) {
          // 验证失败的时候 代码中不需要有什么处理了错误信息已经在页面中展示给用户了
          return false;
        }
        // 表单验证成功
        // 1 获取到用户名和密码
        // console.log(this.loginForm);
        // 2 调用登录接口 完成登录
        // 登录接口地址：http://localhost:8888/api/private/v1/login
        axios
          .post("http://localhost:8888/api/private/v1/login", this.loginForm)
          .then(res => {
            console.log(res);
            if (res.data.meta.status === 200) {
              // 将 token 存储到localStorage中
              // 注意 先存储token 再跳转路由
              localStorage.setItem("token", res.data.data.token);
              // 登录成功
              // 3 成功后 才跳转到首页
              this.$router.push({ name: "home" });
            } else {
              // 登录失败 提示用户错误信息
              // alert(res.data.meta.msg);
              this.$message({
                message: res.data.meta.msg,
                type: "error",
                duration: 1000
              });
            }
          });
      });
    },
    resetForm(formName) {
      this.$refs[formName].resetFields();
    }
  }
};
</script>

<style>
</style>
