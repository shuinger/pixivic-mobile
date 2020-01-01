<template>
  <transition enter-active-class="animated zoomIn"
              leave-active-class="animated rollOut"
              :duration="200">
    <div class="detail">
      <Scroll v-if="illustDetail"
              :data="pictureList"
              ref="scroll"
              :options="options"
              :loading="loading"
              @pulling-up="onPullingUp">
        <div class="detail__top animated zoomIn">
          <div class="top__img"
               :style="`height: ${illustDetail.itemHeight}px`"
               @click="showImagePreview">
            <img v-lazy="PREFIX + illustDetail.imageUrls[0].large.replace('_webp', '')"
                 alt="" />
          </div>
          <div class="top__content">
            <h2 class="title">{{illustDetail.title}}</h2>
            <div class="artist">
              <img :src="PREFIX + illustDetail.artistPreView.avatar"
                   @click="goArtist"
                   alt="" />
              <h2>{{illustDetail.artistPreView.name}}</h2>
            </div>
            <div class="time">
              <span>
                {{illustDetail.createDate | formatCreateDate}}
              </span>
              <span>{{illustDetail.totalView}} 阅读</span>
              <span>
                <span>{{illustDetail.totalBookmarks}} </span>喜欢!
              </span>
            </div>
          </div>
        </div>
        <h2 class="detail__middle"
            v-if="pictureList.length">相关作品</h2>
      </Scroll>
      <Bottom :detail="illustDetail"
              @handleClick="handleClick" />
    </div>
  </transition>
</template>

<script>
import dayjs from 'dayjs'
import Scroll from '@/components/scroll/Scroll'
import Bottom from './bottom/Bottom'

export default {
  name: 'Detail',
  components: {
    Bottom,
    Scroll
  },
  props: {
    pid: {
      required: true,
      type: String
    }
  },
  data () {
    return {
      pictureList: [],
      illustDetail: null,
      illustType: 'illust',
      loading: false,
      page: 1,
      noData: false
    }
  },
  computed: {
    imgs () {
      return this.illustDetail.imageUrls.reduce((pre, cur) => {
        return pre.concat(`${this.PREFIX + cur.original}`)
      }, [])
    },
    options () {
      return {
        pullUpLoad: {
          threshold: 0,
          txt: { more: '上拉加载更多', noMore: '(￣ˇ￣)俺也是有底线的' },
          visible: false
        },
        scrollbar: true,
        probeType: 3
      }
    }
  },
  mounted () {
    // router-view 设置了key 所以每次进来不同的key都会执行
    this.getIllustDetail()
    this.getRelatedList()
  },
  methods: {
    getIllustDetail () {
      this.loading = true
      this.$api.detail
        .reqIllustDetail(this.pid)
        .then(res => {
          this.illustDetail = res.data.data
          const itemHeight = parseInt((this.illustDetail.height / this.illustDetail.width) * window.innerWidth)
          this.illustDetail.itemHeight = itemHeight
          this.loading = false
        })
    },
    getRelatedList () {
      if (this.noData) {
        return this.$refs.scroll.forceUpdate()
      }
      const data = {
        page: this.page++,
        illustId: this.pid
      }
      this.$api.detail
        .reqRelatedIllust(data)
        .then(res => {
          console.log(res)
          if (!res.data.data) {
            this.$refs.scroll.forceUpdate()
            this.noData = true
            return
          }
          this.pictureList = this.pictureList.concat(res.data.data)
        })
    },
    showImagePreview () {
      this.$createImagePreview({
        imgs: this.imgs
      }).show()
    },
    handleClick () {
      this.$router.back()
    },
    onPullingUp () {
      this.getRelatedList()
    },
    goArtist () {
      this.$router.push(`/artist/${this.illustDetail.artistId}`)
    }
  },
  filters: {
    formatCreateDate (val) {
      return dayjs(val).format('YYYY-MM-DD HH:mm')
    }
  }
  // 会出现 前进后退都是 Detail 的情况
  // beforeRouteLeave (to, from, next) {
  //   console.log('to', to)
  //   console.log('from', from) // Detail
  //   let cache = this.$vnode.parent.componentInstance.cache
  //   let keys = this.$vnode.parent.componentInstance.keys
  //   console.log('cache', cache) // {/dailyRank: VNode, /search: VNode, /detail/48233212: VNode}
  //   console.log('keys', keys) // ["/dailyRank", "/search", "/detail/48233212"]
  // }
}
</script>
<style lang="stylus" scoped>
@import '~@/style/color.styl'
@import '~@/style/global.styl'
.detail
  position fixed
  top 0
  left 0
  bottom 60px
  background-size contain
  width 100vw
  background-color #fff
  z-index 3
  font-size 16px
  &__top
    width 100%
    background $white
    // border-bottom 1px solid #cccccc
    transition all 0.2s
    .top__img
      max-height 500px
      text-align center
      position relative
      overflow hidden
      // background-color #5c5c5c
      display flex
      justify-content center
      align-items center
      >img
        width 100%
        height 100%
        // max-height 600px
        vertical-align middle
        transition opacity 0.3s
        opacity 1
        object-fit cover
      img[lazy=loading]
        // transform scale(0.3)
        width 100px
        height 100px
    .top__content
      padding 10px
      .title
        font-size 18px
        margin-bottom 8px
        no-wrap()
      .subtitle
        font-size 16px
        color $primary
        margin-bottom 8px
      .artist
        display flex
        align-items center
        margin-bottom 8px
        img
          width 40px
          height 40px
          border-radius 50%
          margin-right 5px
        >h2
          flex 1
          font-size 16px
      .time
        display flex
        align-items center
        // justify-content space-between
        font-size 14px
        color #cccccc
        >span
          flex 1
          text-align center
          &:last-child
            span
              color $primary
  &__middle
    text-align center
    height 40px
    line-height 40px
    font-size 20px
    border-bottom 1px solid #cccccc
    margin-bottom 10px
</style>