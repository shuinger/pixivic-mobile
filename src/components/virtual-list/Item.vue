<template>
  <div class="item" @click="goDetail">
    <div class="item-content" :style="column.style">
      <img
        :src="column.src"
        :style="{opacity}"
        @load="handleLoad"
      >
      <div class="img-filter" />
      <div v-if="column.pageCount > 1" class="count">
        <img src="../../assets/images/count.svg">
        <span>{{ column.pageCount }}</span>
      </div>
      <Like v-if="!column.isad" :like="column.isLiked" @handleLike="handleLike" />
      <div v-if="column.setu" class="setu-filter">
        <svg font-size="50" class="icon" aria-hidden="true">
          <use xlink:href="#picsuo2" />
        </svg>
      </div>
    </div>
  </div>
</template>

<script>
import Like from '@/components/like/Like';

export default {
  name: 'Item',
  components: {
    Like
  },
  props: {
    column: {
      type: Object,
      required: true
    }
  },
  data() {
    return {
      opacity: 0
    };
  },
  methods: {
    handleLike() {
      this.$emit('handleLike', this.column);
    },
    handleLoad() {
      if (!this.column.setu) {
        this.opacity = 1;
      }
    },
    goDetail() {
      if (this.column.isad) {
        window.open(this.column.link);
      } else {
        this.$store.dispatch('setDetail', this.column);
        this.$router.push(`/detail/${this.column.id}`);
      }
    }
  }
};
</script>

<style lang="stylus" scoped>
@import '~@/assets/style/global.styl'
.item
  box-sizing border-box
  position absolute
  top 0
  left 0
  width 100%
  height 100%
  padding 8px
  overflow hidden
  .item-content
    position relative
    width 100%
    height 100%
    border-radius 8px
    img
      width 100%
      height 100%
      transition opacity .3s
      object-fit cover
      border-radius 8px
    .img-filter
      position absolute
      top 0
      right 0
      bottom 0
      left 0
      background rgba(0, 0, 0, .03)
      border-radius 8px
  .count
    position absolute
    display inline-block
    top 8px
    right 8px
    color white
    background-color #0000009e
    padding 2px
    border-radius 4px
    img
      float left
      fill white
      height 20px
      width 20px
    span
      float right
      padding 0 2px
      line-height 20px
  .setu-filter
    position absolute
    top 0
    right 0
    bottom 0
    left 0
    width 50px
    height 50px
    margin auto
    z-index 2
</style>
