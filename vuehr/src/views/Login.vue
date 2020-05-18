<template>
  <div class="app">
    <h1 class="title">人力资源管理系统</h1>
    <el-form
      :rules="rules"
      ref="loginForm"
      v-loading="loading"
      element-loading-text="正在登录..."
      element-loading-background="rgba(255, 255, 255, 0.8)"
      :model="loginForm"
      class="loginContainer"
    >
      <el-form-item prop="username">
        <el-input
          size="normal"
          type="text"
          v-model="loginForm.username"
          auto-complete="off"
          placeholder="请输入用户名"
        ></el-input>
      </el-form-item>
      <el-form-item prop="password">
        <el-input
          size="normal"
          type="password"
          v-model="loginForm.password"
          auto-complete="off"
          placeholder="请输入密码"
        ></el-input>
      </el-form-item>
      <el-form-item prop="code">
        <el-input
          size="normal"
          type="text"
          v-model="loginForm.code"
          auto-complete="off"
          placeholder="点击图片更换验证码"
          @keydown.enter.native="submitLogin"
          style="width: 160px"
        ></el-input>
        <img :src="vcUrl" @click="updateVerifyCode" alt class="img-code" />
      </el-form-item>
      <el-button size="normal" type="primary" style="width: 100%;" @click="submitLogin">登录</el-button>
    </el-form>
  </div>
</template>

<script>
export default {
  name: "Login",
  data() {
    return {
      loading: false,
      vcUrl: "/verifyCode?time=" + new Date(), // 不读缓存
      loginForm: {
        username: "admin",
        password: "123",
        code: ""
      },
      rules: {
        username: [
          { required: true, message: "请输入用户名", trigger: "blur" }
        ],
        password: [{ required: true, message: "请输入密码", trigger: "blur" }],
        code: [{ required: true, message: "请输入验证码", trigger: "blur" }]
      }
    };
  },
  methods: {
    updateVerifyCode() {
      this.vcUrl = "/verifyCode?time=" + new Date();
    },
    submitLogin() {
      this.$refs.loginForm.validate(valid => {
        if (valid) {
          this.loading = true;
          this.postRequest("/doLogin", this.loginForm).then(resp => {
            this.loading = false;
            if (resp) {
              this.$store.commit("INIT_CURRENTHR", resp.obj);
              window.sessionStorage.setItem("user", JSON.stringify(resp.obj));
              let path = this.$route.query.redirect;
              this.$router.replace(
                path == "/" || path == undefined ? "/home" : path
              );
            } else {
              this.vcUrl = "/verifyCode?time=" + new Date();
            }
          });
        } else {
          return false;
        }
      });
    }
  }
};
</script>

<style scoped>
.app {
  height: calc(100vh);
  background-image: url("../../public/bg.jpg");
  background-size: auto 100%;
  background-repeat: no-repeat;
}
.title {
  font-size: 45px;
  color: rgba(255, 255, 255, 0.8);
  text-shadow: 0 0 20px #eee;
  position: absolute;
  left: 50%;
  transform: translateX(-50%);
  top: 40px;
}
.loginContainer {
  border-radius: 8px;
  background-clip: padding-box;
  width: 260px;
  padding: 25px 35px 25px 35px;
  background: rgba(255, 255, 255, 0.8);
  border: 1px solid #eaeaea;
  box-shadow: 0 0 25px #cac6c6;
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
}
.img-code {
  cursor: pointer;
  width: 90px;
  height: 40px;
  padding-left: 10px;
  vertical-align: bottom;
}
</style>
