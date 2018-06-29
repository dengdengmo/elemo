<template>
  <div>
    <v-header :seller="seller"></v-header>
    <div class="tab">
      <div class="tab-item">
        <router-link to="/goods">商品</router-link>
      </div>
      <div class="tab-item">
        <router-link to="/ratings">评论</router-link>
      </div>
      <div class="tab-item">
        <router-link to="/seller">商家</router-link>
      </div>
    </div>
    <router-view :seller="seller" keep-alive></router-view>
  </div>
</template>

<script>
import {urlParse} from 'common/js/util.js'
import header from 'components/header/header.vue'
import 'common/js/iconfont.js'

const ERR_OK = 0

export default {
  data () {
    return {
      seller: {
        id: (() => {
          let queryParam = urlParse()
          return queryParam.id
        })()
      }
    }
  },
  created() {
    this.$http.get('/api/seller?id=' + this.seller.id).then((response) => {
      response = response.body
      if (response.errno === ERR_OK) {
        this.seller = Object.assign({}, this.seller, response.data)
      }
    })
  },
  name: 'App',
  components: {
    'v-header': header
  }
}
</script>

<style lang="scss" scoped>
  .tab {
    display: flex;
    width: 100%;
    height: 60px;
    line-height: 60px;
    .tab-item {
      flex: 1;
      text-align: center;
      & > a {
        display: block;
        font-size: 14px;
        color: rgb(77, 85, 93);
        &.active{
          color: rgb(240, 20, 20)
        }
      }
    }
  }
</style>
