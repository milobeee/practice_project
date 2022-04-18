<template>
  <div class="home-container">
    <van-nav-bar title="每日笔记" fixed />
    <van-pull-refresh v-model="isLoading" :disabled='finished' @refresh="onRefresh">
      <van-list v-model="loading" :finished="finished" finished-text="没有更多了" @load="onLoad">
        <ArticleInfo
          v-for="item in artlist"
          :key="item.id"
          :title="item.title"
          :author="item.aut_name"
          :cmtCount="item.comm_count"
          :time="item.pubdate"
          :cover="item.cover"
        ></ArticleInfo>
      </van-list>
    </van-pull-refresh>
  </div>
</template>

<script>
import { getArticleListAPI } from '@/api/articleAPI.js'
import ArticleInfo from '@/components/Article/ArticleInfo.vue'

export default {
  name: 'Home-item',
  data () {
    return {
      page: 1,
      limit: 10,
      artlist: [],
      // 是否正在加载下一页数据，如果为 true，则不会反复触发 load 事件
      loading: true,
      // 所有数据是否加载完毕了，如果没有更多数据了，把它改为 true
      finished: false,

      isLoading: false
    }
  },
  created () {
    this.initArticleList()
  },
  methods: {
    async initArticleList (isRefresh) {
      const { data: res } = await getArticleListAPI(this.page, this.limit)
      if (isRefresh) {
        this.artlist = [...res, ...this.artlist]
        this.isLoading = false
      } else {
        this.artlist = [...this.artlist, ...res]
        this.loading = false
      }
      if (res.length === 0) {
        this.finished = true
      }
    },
    onLoad () {
      this.page++
      this.initArticleList()
    },
    onRefresh () {
      this.page++
      this.initArticleList(true)
    }
  },
  components: {
    ArticleInfo
  }
}

</script>

<style lang="less" scoped>
.home-container {
  padding: 46px 0 50px 0;
  .van-nav-bar {
    background-color: orange;
  }
  /deep/ .van-nav-bar__title {
    color: white;
  }
}
</style>
