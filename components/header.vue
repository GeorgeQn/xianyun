<template>
  <div class="container">
    <el-row type="flex" class="main" justify="space-between">

        <div class="logo">
            <img src="http://157.122.54.189:9093/images/logo.jpg" alt="">
        </div>

        <el-row type="flex" class="navs">
            <nuxt-link to='/'>首页</nuxt-link>
            <nuxt-link to='/post'>旅游攻略</nuxt-link>
            <nuxt-link to='/hotel'>酒店</nuxt-link>
            <nuxt-link to='/air'>国内机票</nuxt-link>
        </el-row>

        <div class="login" v-if="!$store.state.user.userInfo.token">
            <nuxt-link to='/user/login'>登录/注册</nuxt-link>
        </div>

        <div v-else>
            <!-- <img src={{$store.state.user.userInfo.user.defaultAvatar}} > -->
            
            <el-dropdown>
                <span class="el-dropdown-link">
                    <img :src="`${$axios.defaults.baseURL}${$store.state.user.userInfo.user.defaultAvatar}`" >
                    <span class="el-dropdown-link">
                        <span>{{$store.state.user.userInfo.user.nickname}}</span>
                        <i class="el-icon-arrow-down el-icon--right"></i>
                    </span>
                </span>
                <el-dropdown-menu slot="dropdown" >
                    <el-dropdown-item>个人中心</el-dropdown-item>
                    <el-dropdown-item @click.native="handleLogout">退出</el-dropdown-item>
                </el-dropdown-menu>
            </el-dropdown>
        </div>

    </el-row>
  </div>
</template>

<script>
export default {
    methods:{
        handleLogout(){
            this.$store.commit('user/clearUserInfo')
            this.$message({
                type:'success',
                message:"退出成功"
            })
        }
    },
};
</script>

<style lang="less" scoped>
    .container{
        height:60px;
        line-height: 60px;
        line-height: 60px;
        border-bottom: 1px #ddd solid;
        box-shadow: 0 2px 2px #ddd;
    }

    .main{
        width: 1000px;
        margin:0 auto
    }
    .logo{
        padding-top: 9px;
        img {
            width:156px;
            height:42px;
            display: block;
        }
    }
    .navs{
        flex :1;
        margin-left:10px;
        a{
            display: block;
            height: 60px;
            padding: 0 20px;
            box-sizing: border-box;

            &:hover{
                color: #409eff;
                border-bottom:5px #409eff solid;
            }
        }
        .nuxt-link-exact-active{
        background: #409eff;
        color: #fff;
        &:hover{
            color: #fff;
        }
    }
    }
    .el-dropdown-link img{
        width: 36px;
        height: 36px;
        vertical-align: middle;
        border-radius: 50%;
        border:white 2px solid;
        box-sizing: border-box;//?
        &:hover{
            border:#409eff 2px solid;
        }
    }
    
</style>