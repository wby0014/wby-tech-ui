<template>
  <div class="app-container documentation-container">
    <a class="document-btn" target="_blank" href="https://cn.vuejs.org/">VUE</a>
    <a class="document-btn" target="_blank" href="https://github.com/wby0014/wby-springboot-dubbo">Github Repository</a>
    <a class="document-btn" target="_blank" href="https://github.com/wby0014/wby-tech-ui">wby tech ui</a>
    <dropdown-menu class="document-btn" :items="articleList" title="系列文章" />
    <a class="document-btn" target="_blank" href="https://github.com/wby0014/">内推招聘</a>
  </div>
</template>

<script>
import DropdownMenu from '@/components/Share/DropdownMenu'

export default {
  name: 'Documentation',
  components: { DropdownMenu },
  data() {
    return {
      articleList: [
        { title: '基础篇', href: 'https://juejin.im/post/59097cd7a22b9d0065fb61d2' }
      ]
    }
  }
}
</script>

<style lang="scss" scoped>
.documentation-container {
  margin: 50px;
  display: flex;
  flex-wrap: wrap;
  justify-content: flex-start;

  .document-btn {
    flex-shrink: 0;
    display: block;
    cursor: pointer;
    background: black;
    color: white;
    height: 60px;
    padding: 0 16px;
    margin: 16px;
    line-height: 60px;
    font-size: 20px;
    text-align: center;
  }
}
</style>
