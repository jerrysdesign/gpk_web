<template lang="pug">
#topics
  .header-banner(:style="{backgroundImage: 'url(' + topic.banner_url + ')'}")
    template(v-if="showtitle")
      h3 # {{topic.title}} #
      .desc 共{{topic.post_count}}篇文章
  .main-content
    .container
      .article-list
        item(v-for="post in posts", :key="post.id", :post="post")
        .tac
          a.load-more(@click="fetch", :class="{'loading-in': loading, 'no-more': nomore}")
            .loading-article
            span 加载更多
      .article-sidebar
        hotnews(v-once)
</template>

<script>
import Item from './posts/Item.vue'
import Hotnews from './posts/Hotnews.vue'
import api from 'store/api'

export default {
  components: { Item, Hotnews },
  data () {
    return {
      page: 0,
      loading: true,
      nomore: false,
      showtitle: true,
      topic: {},
      posts: []
    }
  },
  meta () {
    return {
      title: "专题精选"
    }
  },
  methods: {
    fetch () {
      this.loading = true
      this.page += 1
      api.get(`topics/${this.$route.params.id}?page=${this.page}`).then((result) => {
        let data = result.data.topic
        if (data.id == 277 || data.id == 279) {
          this.showtitle = false
        }
        this.topic = data
        this.posts = this.posts.concat(data.posts)
        document.title = data.title + ' | 极客公园'
        if (!data.posts.length || (data.posts.length === data.post_count)) this.nomore = true
        this.loading = false
      }).catch((err) => {
        this.$message.error(err.toString())
      })
    },
  },

  beforeMount () {
    this.fetch()
  }
}

</script>

<style lang="stylus" scoped>
.header-banner
  background url('//imgslim.geekpark.net/image/newgeekpark/column_bg.jpg') center center no-repeat
  background-size cover
  color #fff
  text-align center
  padding 20px 0
  height 180px
  h3
    margin 40px 0 40px
    font-size 50px
    font-weight 300
    letter-spacing 0
    text-indent 0.25em
  .desc
    font-size 14px
    font-weight 300
    letter-spacing 0
    padding 0 60px
    line-height 1.5
  @media screen and (max-width: 767px)
    h3
      font-size 40px
      letter-spacing 0
      text-indent 0
    .desc
      letter-spacing 0
</style>
