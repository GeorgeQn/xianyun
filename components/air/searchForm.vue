<template>
  <div class="search-form">
    <!-- 头部tab切换 -->
    <el-row type="flex" class="search-tab">
      <span
        v-for="(item, index) in tabs"
        :key="index"
        @click="handleSearchTab(index)"
        :class="{active: index === currentTab}"
      >
        <i :class="item.icon"></i>
        {{item.name}}
      </span>
    </el-row>

    <el-form class="search-form-content" ref="form" label-width="80px">
      <el-form-item label="出发城市">
        <!-- fetch-suggestions 返回输入建议的方法 -->
        <!-- select 点击选中建议项时触发 -->
        <el-autocomplete
          :fetch-suggestions="queryDepartSearch"
          placeholder="请搜索出发城市"
          @select="handleDepartSelect"
          class="el-autocomplete"
          v-model="form.departCity"
        ></el-autocomplete>
      </el-form-item>
      <el-form-item label="到达城市">
        <el-autocomplete
          :fetch-suggestions="queryDestSearch"
          placeholder="请搜索到达城市"
          @select="handleDestSelect"
          class="el-autocomplete"
          v-model="form.destCity"
        ></el-autocomplete>
      </el-form-item>
      <el-form-item label="出发时间">
        <!-- change 用户确认选择日期时触发 -->
        <el-date-picker type="date" placeholder="请选择日期" style="width: 100%;" @change="handleDate" v-model="form.departDate"></el-date-picker>
      </el-form-item>
      <el-form-item label>
        <el-button style="width:100%;" type="primary" icon="el-icon-search" @click="handleSubmit">搜索</el-button>
      </el-form-item>
      <div class="reverse">
        <span @click="handleReverse">换</span>
      </div>
    </el-form>
  </div>
</template>

<script>
    import moment from 'moment'
export default {
  data() {
    return {
      tabs: [
        { icon: "iconfont icondancheng", name: "单程" },
        { icon: "iconfont iconshuangxiang", name: "返程" }
      ],
      currentTab: 0,
      form: {
        departCity: "上海",
        departCode: ".",
        destCity: "广州",
        destCode: "",
        departDate: ""
      }
    };
  },
  methods: {
    handleSearchTab(index) {
      if (index === 1) {
        this.$alert("抱歉,暂时没有这项功能");
      }
    },
    handleDepartSelect() {},
    //修改时间格式
    handleDate(value) {
        // console.log(this.form.departDate)
        this.form.departDate = moment(value).format(`YYYY-MM-DD`)
    },
    handleSubmit() {
      //搜索
        // console.log(this.form)
        const {departCity, destCity, departDate} = this.form;
            // 判断输入框不能为空
            if(!departCity){
                this.$alert("出发城市不能为空", "提示");
                return;
            }
            if(!destCity){
                this.$alert("到达城市不能为空", "提示");
                return;
            }
            if(!departDate){
                this.$alert("出发日期不能为空", "提示");
                return;
            }
        const arr = JSON.parse(localStorage.getItem("airs")) || [];
        arr.push(this.form);
            // 把搜索的条件保存到本地
        localStorage.setItem("airs", JSON.stringify(arr));

        this.$axios({
            url:'/airs',
            params:this.form
        }).then(res=>{
            this.$router.push({
                path:'/air/fights',
                query:this.form
            })
        })
    },


    handleReverse() {
        const {departCity,departCode,destCity,destCode} = this.form

        this.form.departCity = destCity
        this.form.departCode = destCode

        this.form.destCity = departCity
        this.form.destCode = departCode
    },


    queryDepartSearch(value, cb) {
      //输入框没有值
      if (!value) {
        cb([]);
        return;
      }
      this.$axios({
        url: "/airs/city",
        params: {
          name: value
        }
      }).then(res => {
        //解构
        console.log(res)
        const data = res.data.data;
        const newData = [];
        data.forEach(item => {
          item.value = item.name.replace("市", "");
          newData.push(item);
        });
        //默认选择第一行
        this.form.departCity = newData[0].value
        this.form.departCode = newData[0].sort
        cb(newData);
      });
    },
    queryDestSearch(value, cb) {
      if (!value) {
        cb([]);
        return;
      }
      this.$axios({
        url: "/airs/city",
        params: {
          name: value
        }
      }).then(res => {
        //解构
        const data = res.data.data;
        const newData = [];
        data.forEach(item => {
          item.value = item.name.replace("市", "");
          newData.push(item);
        });
        //默认选择第一行
        this.form.destCity = newData[0].value
        this.form.destCode = newData[0].sort
        cb(newData);
      });
    },
    handleDestSelect() {}
  }
};
</script>

<style scoped lang="less">
.search-form {
  border: 1px #ddd solid;
  border-top: none;
  width: 360px;
  height: 350px;
  box-sizing: border-box;
}

.search-tab {
  span {
    display: block;
    flex: 1;
    text-align: center;
    height: 48px;
    line-height: 42px;
    box-sizing: border-box;
    border-top: 3px #eee solid;
    background: #eee;
  }

  .active {
    border-top-color: orange;
    background: #fff;
  }

  i {
    margin-right: 5px;
    font-size: 18px;

    &:first-child {
      font-size: 16px;
    }
  }
}

.search-form-content {
  padding: 15px 50px 15px 15px;
  position: relative;

  .el-autocomplete {
    width: 100%;
  }
}

.reverse {
  position: absolute;
  top: 35px;
  right: 15px;

  &:after,
  &:before {
    display: block;
    content: "";
    position: absolute;
    left: -35px;
    width: 25px;
    height: 1px;
    background: #ccc;
  }

  &:after {
    top: 0;
  }

  &:before {
    top: 60px;
  }

  span {
    display: block;
    position: absolute;
    top: 20px;
    right: 0;
    font-size: 12px;
    background: #999;
    color: #fff;
    width: 20px;
    height: 20px;
    line-height: 18px;
    text-align: center;
    border-radius: 2px;
    cursor: pointer;

    &:after,
    &:before {
      display: block;
      content: "";
      position: absolute;
      left: 10px;
      width: 1px;
      height: 20px;
      background: #ccc;
    }

    &:after {
      top: -20px;
    }

    &:before {
      top: 20px;
    }
  }
}
</style>