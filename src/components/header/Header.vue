<template>
  <div class="header">
    <div class="header-container" :style="{background}">
      <svg font-size="20" class="icon" aria-hidden="true" @click="handleClick">
        <use xlink:href="#picfanhui1" />
      </svg>
      <h1>{{ title }}</h1>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    title: {
      type: String,
      default: '标题'
    },
    background: {
      type: String,
      default: 'rgba(255, 255, 255, 0.95)'
    }
  },
  methods: {
    handleClick() {
      this.$router.back();
      this.$emit('handleClick');
    }
  }
};
</script>

<style lang="stylus" scoped>
.header
  position relative
  z-index 102
  .header-container
    position relative
    top 0
    padding 5px 10px
    height 50px
    width 100%
    display flex
    line-height 40px
    box-sizing border-box
    color #333
    font-size 16px
    transition top .2s
    >svg
      position absolute
      left 5px
      top 15px
      font-size 20px
      width 20px
    >h1
      font-size 18px
      font-weight 700
      margin 0 auto
</style>
