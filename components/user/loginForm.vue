<template>
  <el-form :model="form" ref="form" :rules="rules" class="form">
    <el-form-item class="form-item" prop="username">
      <el-input placeholder="用户名/手机" v-model="form.username"></el-input>
    </el-form-item>
    <el-form-item class="form-item" prop="password">
      <el-input placeholder="密码" v-model="form.password" type="password"></el-input>
    </el-form-item>

    <p class="form-text">
      <nuxt-link to="#">忘记密码</nuxt-link>
    </p>
    <!-- 登录按钮 -->
    <el-button class="submit" type="primary" @click="handleLoginSubmit">登录</el-button>
  </el-form>
</template>

<script>
export default {
  data() {
    return {
      form: {
        username: "",
        password: ""
      },
      rules: {
        username: [
          { required: true, message: "请输入用户名", trigger: "blur" }
        ],
        password: [{ required: true, message: "请输入用密码", trigger: "blur" }]
      }
    };
  },
  methods: {
    handleLoginSubmit() {
        this.$refs.form.validate((valid) => {
          if (valid) {
            console.log(this.form)
            this.$axios({
                url:'/accounts/login',
                data:this.form,
                method:'POST'
            }).then(res=>{
                console.log(res)
                this.$store.commit('user/setUserInfo',res.data)
                this.$message({
                  message:'成功登录,正在为你跳转到首页',
                  type:'success'
                })
                this.$router.push({
                  path:'/'
                })
            }).catch(err=>{
              console.log(err)
            })
            
          } else {
            console.log('验证失败');
          }
        });
    }
  },
  mounted() {
    console.log(this.$store.state.user.userInfo.token)
  }
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

.submit {
  width: 100%;
  margin-top: 10px;
}
</style>