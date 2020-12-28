<template>
  <div class="site-wrapper site-page--not-found">
    <div class="img-dd">
      <img :src="errGif" width="213" height="328" alt="Girl has dropped her ice cream.">
    </div>
    <div class="site-content__wrapper">
      <div class="site-content">
        <h2 class="not-found-title">404</h2>
        <p class="not-found-desc">抱歉！您访问的页面<em>失联</em>啦 ...</p>
        <el-button @click="$router.go(-1)">返回上一页</el-button>
        <el-button type="primary" class="not-found-btn-gohome" @click="$router.push({ name: 'home' })">进入首页</el-button>
        <el-button type="primary" @click.prevent="dialogVisible=true">点我抽奖</el-button>

        <el-dialog :visible.sync="dialogVisible" title="随便看">
           <img :src="ewizardClap" class="pan-img">
        </el-dialog>
      </div>
    </div>
    
    
  </div>
</template>

<script>
  import errGif from '@/assets/401_images/401.gif'

export default {
  name: 'Page401',
  data() {
    return {
      errGif: errGif + '?' + +new Date(),
      ewizardClap: 'https://wpimg.wallstcn.com/007ef517-bafd-4066-aae4-6883632d9646',
      dialogVisible: false
    }
  },
  methods: {
    back() {
      if (this.$route.query.noGoBack) {
        this.$router.push({ path: '/dashboard' })
      } else {
        this.$router.go(-1)
      }
    }
  }
}
</script>

<style lang="scss">
  .site-wrapper.site-page--not-found {
    position: absolute;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
    overflow: hidden;

    .site-content__wrapper {
      padding: 0;
      margin: 0;
      background-color: #fff;
    }

    .site-content {
      position: fixed;
      top: 15%;
      left: 50%;
      z-index: 2;
      padding: 30px;
      text-align: center;
      transform: translate(-50%, 0);
    }

    .not-found-title {
      margin: 20px 0 15px;
      font-size: 10em;
      font-weight: 400;
      color: rgb(55, 71, 79);
    }

    .not-found-desc {
      margin: 0 0 30px;
      font-size: 26px;
      text-transform: uppercase;
      color: rgb(118, 131, 143);

      > em {
        font-style: normal;
        color: #ee8145;
      }
    }

    .not-found-btn-gohome {
      margin-left: 30px;
    }
  }

  .pan-img {
      display: block;
      margin: 0 auto;
      width: 100%;
    }

    .img-dd {
      padding-top: 130px;
      padding-left: 200px;
    }
</style>
