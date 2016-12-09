<template>
  <div class="news-view">
    <spinner :show="loading"></spinner>    
    <div class="news-list-nav">
      <router-link to="/">Today</router-link>
      <router-link to="today">Yesterday</router-link>
      <router-link to="today">Specific day</router-link>
    </div>
    <transition :name="transition">
      <div class="news-list" :key="displayedPage" v-if="displayedPage > 0">
        <h2 class="dateHeader">
          <span class="time">Thursday, December 8th 2016</span>
        </h2>
        <div class="dirLinks">
          <router-link to="up" class="uplink">Up</router-link>
          <router-link to="down" class="downlink">Down</router-link>
        <div>
        <transition-group tag="ul" name="item">
          <item v-for="item in displayedItems" :key="item.id" :item="item">
          </item>
        </transition-group>
      </div>
    </transition>
  </div>
</template>

<script>
import Spinner from '../components/Spinner.vue'
import Item from '../components/Item.vue'
import { watchList } from '../store/api'

export default {
  name: 'stock-list',
  // this will be called during SSR to pre-fetch data into the store!
  preFetch (store) {
    return store.dispatch('FETCH_LIST_DATA', { type: 'top' })
  },
  components: {
    Spinner,
    Item
  },

  props: {
    type: String
  },

  data () {
    const isInitialRender = !this.$root._isMounted
    return {
      loading: false,
      transition: 'slide-up',
      // if this is the initial render, directly render with the store state
      // otherwise this is a page switch, start with blank and wait for data load.
      // we need these local state so that we can precisely control the timing
      // of the transitions.
      displayedPage: isInitialRender ? Number(this.$store.state.route.params.page) || 1 : -1,
      displayedItems: [ 
      { 
        id:13130711,
        type:"story", 
        by:"JumpCrisscross", 
        comments:186, 
        symbol:"WYNN", 
        direction: "down",
        percent: "11.05",
        time:1481209703,
        stories:[ 
          { title: "Fears of Beijing gambling crackdown sends casino stocks crashing", url: "http://www.reuters.com/article/us-china-gaming-idUSKBN13Y05V?feedType=RSS&feedName=businessNews&utm_source=Twitter&utm_medium=Social&utm_campaign=Feed%3A+reuters%2FbusinessNews+%28Business+News%29"},
          { title: "What's the financial impact As China Caps ATM Withdrawal?", url: "http://blogs.barrons.com/asiastocks/2016/12/08/macau-gaming-whats-the-impact-as-china-caps-atm-withdrawal/"}
        ]
      },
      { 
        id:13130712,
        type:"story", 
        by:"JumpCrisscross", 
        comments:186, 
        symbol:"WYNN", 
        direction: "up",
        percent: "11.05",
        time:1481209703,
        stories:[ 
          { title: "Fears of Beijing gambling crackdown sends casino stocks crashing", url: "http://www.reuters.com/article/us-china-gaming-idUSKBN13Y05V?feedType=RSS&feedName=businessNews&utm_source=Twitter&utm_medium=Social&utm_campaign=Feed%3A+reuters%2FbusinessNews+%28Business+News%29"},
          { title: "What's the financial impact As China Caps ATM Withdrawal?", url: "http://blogs.barrons.com/asiastocks/2016/12/08/macau-gaming-whats-the-impact-as-china-caps-atm-withdrawal/"}
        ]
      }
      ] //isInitialRender ? this.$store.getters.activeItems : []
    }
  },

  computed: {
    page () {
      return Number(this.$store.state.route.params.page) || 1
    },
    maxPage () {
      const { itemsPerPage, lists } = this.$store.state
      return Math.ceil(lists[this.type].length / itemsPerPage)
    },
    hasMore () {
      return this.page < this.maxPage
    }
  },

  beforeMount () {
    /*if (this.$root._isMounted) {
      this.loadItems(this.page)
    }
    // watch the current list for realtime updates
    this.unwatchList = watchList(this.type, ids => {
      this.$store.commit('SET_LIST', { type: this.type, ids })
      this.$store.dispatch('ENSURE_ACTIVE_ITEMS').then(() => {
        this.displayedItems = this.$store.getters.activeItems
      })
    }) */
  },

  beforeDestroy () {
    // this.unwatchList()
  },

  watch: {
    page (to, from) {
      this.loadItems(to, from)
    }
  },

  methods: {
    loadItems (to = this.page, from = -1) {
      this.loading = true
      this.$store.dispatch('FETCH_LIST_DATA', {
        type: this.type
      }).then(() => {
        if (this.page < 0 || this.page > this.maxPage) {
          this.$router.replace(`/${this.type}/1`)
          return
        }
        this.transition = from === -1
          ? null
          : to > from ? 'slide-left' : 'slide-right'
        this.displayedPage = to
        this.displayedItems = this.$store.getters.activeItems
        this.loading = false
      })
    }
  }
}
</script>

<style>
  .dateHeader {
    text-align: center;
  }

  .dirLinks {
    text-align: center;
  }

  .dirLinks a {
    margin-right: 10px;
  }
</style>

<style lang="stylus">
.news-list-nav, .news-list
  background-color #fff
  border-radius 2px

.news-list-nav
  padding 15px 30px
  position fixed
  top 55px
  text-align center
  left 0
  right 0
  z-index 998
  box-shadow 0 1px 2px rgba(0,0,0,.1)
  a
    margin 0 1em
  .disabled
    color #ccc

.news-list
  position absolute
  margin 60px 0
  width 100%
  transition all .5s cubic-bezier(.55,0,.1,1)
  ul
    list-style-type none
    padding 0
    margin 0

.slide-left-enter, .slide-right-leave-active
  opacity 0
  transform translate(30px, 0)

.slide-left-leave-active, .slide-right-enter
  opacity 0
  transform translate(-30px, 0)

.item-move, .item-enter-active, .item-leave-active
  transition all .5s cubic-bezier(.55,0,.1,1)

.item-enter
  opacity 0
  transform translate(30px, 0)

.item-leave-active
  position absolute
  opacity 0
  transform translate(30px, 0)

@media (max-width 600px)
  .news-list
    margin 10px 0
</style>
