<template>
  <el-form class="form" :model="registerForm" ref="form" :rules="rules">
    <el-form-item prop="username" class="form-item">
      <el-input placeholder="用户名手机" v-model="registerForm.username"></el-input>
    </el-form-item>

    <el-form-item prop="rulecode" class="form-item">
      <el-input placeholder="验证码" v-model="registerForm.captcha" style="width:237px float:left">
        <template slot="append">
          <!-- 内部实现了调用 this.$emit("click") 触发传递的方法-->
          <el-button @click="handleSendCaptcha">发送验证码</el-button>
        </template>
      </el-input>
    </el-form-item>

    <el-form-item prop="yourname" class="form-item">
      <el-input placeholder="你的名字" v-model="registerForm.nickname"></el-input>
    </el-form-item>

    <el-form-item prop="password" class="form-item">
      <el-input
        placeholder="密码"
        type="password"
        v-model="registerForm.password"
        auto-complete="off"
      ></el-input>
    </el-form-item>

    <el-form-item prop="currentpassword" class="form-item">
      <el-input
        placeholder="确认密码"
        type="password"
        v-model="registerForm.currentpassword"
        auto-complete="off"
      ></el-input>
    </el-form-item>

    <!-- 注册按钮 -->
    <el-button class="register" type="primary" @click="handleResgisterSubmit">注册</el-button>
  </el-form>
</template>

<script>
export default {
  data() {
    var validatePass2 = (rule, value, callback) => {
      if (value === "") {
        callback(new Error("请再次输入密码"));
      } else if (value !== this.registerForm.password) {
        callback(new Error("两次输入密码不一致!"));
      } else {
        callback();
      }
    };

    return {
      registerForm: {
        username: "",
        nickname: "",
        captcha: "",
        password: "",
        currentpassword: ""
      },
      rules: {
        username: [
          { required: true, message: "请输入用户名", trigger: "blur" }
        ],
        captcha: [{ required: true, message: "请输入验证码", trigger: "blur" }],
        nickname: [{ required: true, message: "请输入昵称", trigger: "blur" }],
        password: [{ required: true, message: "请输入密码", trigger: "blur" }],
        currentpassword: [{ validator: validatePass2, trigger: "blur" }]
      }
    };
  },

  methods: {
    handleSendCaptcha() {
      this.$axios({
        url: "/captchas",
        method: "POST",
        data: {
          tel: this.registerForm.username
        }
      }).then(res => {
        this.$message({
          type: "success",
          message: `验证码为${res.data.code}`
        });
      });
    },

    handleResgisterSubmit() {
      console.log(123)
      this.$refs.registerForm.validate(valid => {
        if (valid) {
          //...+变量名指向剩余的属性
          const {currentpassword, ...rest}=this.registerForm
          this.$axios({
            url: "/accounts/register",
            method: "POST",
            data: rest
          }).then(res => {
            this.$store.commit("user/setUserInfo", res.data)
            // this.$router.push({path:'/'})
            // this.$message({
            //   type:'success',
            //   message:'注册成功,正在为你跳转到首页'
            // })
          });
        }
      });
    }
  },
};
</script>

<style scoped lang="less">
.form {
  padding: 25px;
}

.form-item {
  margin-bottom: 20px;
}

.form-text {
  font-size: 12px;
  color: #409eff;
  text-align: right;
  line-height: 1;
}

.register {
  width: 100%;
  margin-top: 10px;
}
</style>