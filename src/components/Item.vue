<template>
  <li class="news-item">
    <div>
      <div :class="'score ' +  item.direction">
        <div>{{ item.symbol }}</div>
        <div>{{ item.percent }}%</div>
      </div>
      <div class="stories">
        <ul class="title">
          <li v-for="story in item.stories">
            <a :href="story.url" target="_blank">{{ story.title }}</a>
            <span class="host"> ({{ story.url | host }})</span>
          </li>
        </ul>
      </div>
    </div>
  </li>
</template>

<script>
import { timeAgo } from '../filters'

export default {
  name: 'news-item',
  props: ['item'],
  // https://github.com/vuejs/vue/blob/next/packages/vue-server-renderer/README.md#component-caching
  serverCacheKey: props => {
    return `${
      props.item.id
    }::${
      props.item.__lastUpdated
    }::${
      timeAgo(props.item.time)
    }`
  }
}
</script>

<style>
  .score.up {
    color: green;
  }

  .score.down {
    color: red;
  }
  .arrow-up {
    width: 0; 
    height: 0; 
    border-left: 10px solid transparent;
    border-right: 10px solid transparent;
    border-bottom: 10px solid green;
    position: absolute;
    left: 0;
    top: 20%;
  }

  .arrow-down {
    width: 0; 
    height: 0; 
    border-left: 15px solid transparent;
    border-right: 10px solid transparent;
    border-top: 15px solid #ff0000;
    position: absolute;
    left: 0;
    top: 20%;
  }

  .stories {
    display: table-cell;
  }
</style>

<style lang="stylus">
.news-item
  background-color #fff
  padding 20px 30px 20px 20px
  border-bottom 1px solid #eee
  position relative
  line-height 20px
  ul
    li
       padding-bottom 10px
  .score
    display table-cell
    font-size 1.1em
    font-weight 700
    width 80px
  .meta, .host
    font-size .85em
    color #999
    a
      color #999
      text-decoration underline
      &:hover
        color #ff6600
</style>
