<template>
  <div class="one__wrapper">
    <div class="one__topper">
      <div class="one__topper-img"></div>
      <div class="one__topper-overlay"></div>
      <div class="one__topper-title">
        <h1>运动员画像</h1>
      </div>
    </div>
    <el-card class="box-card">
      <el-tabs v-model="activeName" @tab-click="handleClick">
        <el-tab-pane label="总览" name="qv">
          <QuickView />
        </el-tab-pane>
        <el-tab-pane label="个人信息" name="first">
          <PersonalInfo />
        </el-tab-pane>
        <el-tab-pane label="专项训练" name="second">
          <ZhuanXiang />
        </el-tab-pane>
        <el-tab-pane label="机能监控" name="third">
          <JiNeng />
        </el-tab-pane>
        <el-tab-pane label="体能" name="fourth">
          <TiNeng />
        </el-tab-pane>
        <el-tab-pane label="伤病" name="fifth">
          <ShangBing />
        </el-tab-pane>
        <el-tab-pane label="竞技表现" name="sixth">
          <JingJi />
        </el-tab-pane>
      </el-tabs>
    </el-card>
  </div>
</template>

<script>
import PersonalInfo from './components/personalInfo'
import ShangBing from './components/shangbing'
import JiNeng from './components/jineng'
import TiNeng from './components/tineng'
import ZhuanXiang from './components/zhuanxiang'
import JingJi from './components/jingji'
import QuickView from './components/quickview'
export default {
  components: {
    PersonalInfo,
    ShangBing,
    JiNeng,
    TiNeng,
    ZhuanXiang,
    JingJi,
    QuickView
  },
  data () {
    return {
      activeName: 'qv'
    }
  },
  mounted () {
  },
  methods: {
    getPersonInfo () {
      axios.get('http://localhost/list/getPersonInfo', {
        params: {
          id: this.$route.params.id
        }
      }).then(res => {
        const d = res.data[0]
        this.personInfo = d
      }).catch(err => {
        console.log('获取数据失败' + err)
      })
    },
    formatDate (d) {
      return formatDate(d)
    },
    getAge (d) {
      return getAge(formatDate(d))
    }
  }
}
</script>

<style lang='less' scoped>
.one {
  &__wrapper {
    width: 100%;
  }
  &__topper {
    height: 600px;
    width: 100%;
    position: relative;
    &-img {
      height: 100%;
      background-image: url('../../assets/images/555.jpg');
      background-position: center;
      background-size: cover;
      background-repeat: no-repeat;
    }
    &-overlay {
      position: absolute;
      bottom: 0;
      left: 0;
      width: 100%;
      height: 100%;
      background: transparent linear-gradient(180deg,rgba(0,57,124,.1),rgba(0,44,94,.3) 22%,rgba(0,37,79,.5) 50%,#001d3e) 0 0 no-repeat padding-box;
    }
    &-title {
      width: 100%;
      position: absolute;
      bottom: 60px;
      left: 0;
      right: 0;
      h1 {
        width: 80%;
        margin: 0 auto;
        font-family: "Effra",Arial,sans-serif;
        font-style: italic;
        font-size: 50px;
        color: white;
        line-height: 75px;
      }
    }
  }
}
.box-card {
  margin: 0 auto;
  width: 95%;
}
</style>
