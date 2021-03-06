<template>
  <div class="ratings" ref="ratings">
    <div class="ratings-content">
      <div class="overview">
        <div class="overview-left">
          <h1 class="score">{{seller.score}}</h1>
          <div class="title">综合评分</div>
          <div class="rank">高于周边商家{{seller.rankRate}}%</div>
        </div>
        <div class="overview-right">
          <div class="score-wrapper">
            <span class="title">服务态度</span>
            <star :size="36" :score="seller.serviceScore"></star>
            <span class="score">{{seller.serviceScore}}</span>
          </div>
          <div class="score-wrapper">
            <span class="title">商品评分</span>
            <star :size="36" :score="seller.foodScore"></star>
            <span class="score">{{seller.foodScore}}</span>
          </div>
          <div class="delivery-wrapper">
            <span class="title">送达时间</span>
            <span class="delivery">{{seller.deliveryTime}}分钟</span>
          </div>
        </div>
      </div>
      <split></split>
      <ratingselect :select-type="selectType" :only-content="onlyContent"
            :ratings="ratings"></ratingselect>
      <div class="rating-wrapper">
        <ul v-show="ratings && ratings.length">
          <li v-show="needShow(rating.rateType,rating.text)" v-for="(rating, index) in ratings" :key="index" class="rating-item">
            <div class="avatar">
              <img width="28px" height="28px" :src="rating.avatar">
            </div>
            <div class="content">
              <h1 class="name">{{rating.username}}</h1>
              <div class="star-wrapper">
                <star :size="24" :score="rating.score"></star>
                <span class="delivery" v-show="rating.deliveryTime">{{rating.deliveryTime}}</span>
                <p class="text">{{rating.text}}</p>
                <div class="recommend" v-show="rating.recommend.length">
                  <svg class="icon icon-thumb_up" aria-hidden="true">
                    <use xlink:href="#icon-thumb_up"></use>
                  </svg>
                  <!-- <span class="item" v-for="item in rating.recommend">{{item}}</span> -->
                </div>
              </div>
              <div class="rateTime">{{rating.rateTime | formatDate}}</div>
            </div>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
import BScroll from 'better-scroll'
import {formatDate} from 'common/js/date'
import ratingselect from 'components/ratingselect/ratingselect'
import split from 'components/split/split'
import star from 'components/star/star'

const ALL = 2
const ERR_OK = 0
export default {
  props: {
    seller: {
      type: Object
    }
  },
  data() {
    return {
      ratings: [],
      selectType: ALL,
      onlyContent: false
    }
  },
  created() {
    this.$http.get('/api/ratings').then((response) => {
      response = response.body
      if (response.errno === ERR_OK) {
        this.ratings = response.data
        this.$nextTick(() => {
          this.scroll = new BScroll(this.$refs.ratings, {
            click: true
          })
        })
      }
    })
    this.$root.eventHub.$on('change-type', this.handleSelectType)
    this.$root.eventHub.$on('content-toggle', this.toggleContent)
  },
  methods: {
    show() {
      this.selectType = ALL
      this.onlyContent = false
      this.$nextTick(() => {
        if (!this.scroll) {
          this.scroll = new BScroll(this.$refs.rating, {
            click: true
          })
        } else {
          this.scroll.refresh()
        }
      })
    },
    handleSelectType(type) {
      this.selectType = type
    },
    toggleContent() {
      this.onlyContent = !this.onlyContent
    },
    needShow(type, text) {
      if (this.onlyContent && !text) {
        return false
      }
      if (this.selectType === ALL) {
        return true
      } else {
        return type === this.selectType
      }
    }
  },
  components: {
    ratingselect,
    split,
    star
  },
  filters: {
    formatDate(time) {
      let date = new Date(time)
      return formatDate(date, 'yyyy-MM-dd hh:mm')
    }
  }
}
</script>

<style lang="scss" scoped>
@import "../../common/sass/mixin.scss";
@import "../../common/sass/base.scss";

.ratings {
  position: absolute;
  top: 174px;
  bottom: 0;
  left: 0;
  width: 100%;
  overflow: hidden;
  .overview {
    display: flex;
    padding: 18px 0;
    .overview-left {
      flex: 0 0 137px;
      padding: 6px 0;
      width: 137px;
      border-right: 1px solid rgba(7, 17, 27, 0.1);
      text-align: center;
      @media only screen and (max-width: 320px) {
        flex: 0 0 120px;
        width: 120px;
      }
      .score {
        margin-bottom: 6px;
        line-height: 28px;
        font-size: 24px;
        color: rgb(255, 153, 0)
      }
      .title {
        margin-bottom: 8px;
        line-height: 12px;
        font-size: 12px;
        color: rgb(7, 17, 27)
      }
      .rank {
        line-height: 10px;
        font-size: 10px;
        color: rgb(147, 153, 159)
      }
    }
    .overview-right {
      flex: 1;
      padding: 6px 0 6px 24px;
      @media only screen and (max-width: 320px) {
        padding-left: 6px;
      }
      .score-wrapper {
        display: inline-block;
        vertical-align: top;
        margin-bottom: 8px;
        line-height: 18px;
        font-size: 0;
        .title {
          font-size: 12px;
          color: rgb(7, 17, 27);
        }
        .star {
          display: inline-block;
          vertical-align: top;
          margin: 0 12px;
        }
        .score {
          display: inline-block;
          vertical-align: top;
          font-size: 12px;
          color: rgb(255, 153, 0);
        }
      }
      .delivery-wrapper {
        font-size: 0;
        .title {
          line-height: 18px;
          font-size: 12px;
          color: rgb(7, 17, 27);
        }
        .delivery {
          margin-left: 12px;
          font-size: 12px;
          color:rgb(147, 153, 159)
        }
      }
    }
  }
  .rating-wrapper {
    padding: 0 18px;
    .rating-item {
      display: flex;
      padding: 18px 0;
      @include border-1px(rgba(7, 17, 27, 0.1));
      .avatar {
        flex: 0 0 28px;
        width: 28px;
        margin-right: 12px;
        img {
          border-radius: 50%;
        }
      }
      .content {
        position: relative;
        flex: 1;
        .name {
          margin-bottom: 4px;
          line-height: 12px;
          font-size: 10px;
          color: rgb(7, 17, 27)
        }
        .star-wrapper {
          margin: 6px 0;
          // font-size: 0;
          .star {
            display: inline-block;
            vertical-align: top;
            margin-right: 16px;
          }
          .delivery {
            display: inline-block;
            vertical-align: top;
            font-size: 10px;
            color: rgb(147, 153, 159);
          }
        }
        .text {
          line-height: 18px;
          color: rgb(7, 17, 27);
          font-size: 12px;
          margin-bottom: 8px;
        }
        .recommend {
          line-height: 16px;
          font-size: 9px;
          .icon-thump_up, .item {
            display: inline-block;
            margin: 0 8px 4px 0;
            font-size: 9px;
          }
          .icon {
            fill: rgb(0, 160, 220)
          }
          .item {
            padding: 0 6px;
            border: 1px solid ;
            border-radius: 1px;
            color: rgb(147, 153, 159);
            background: #fff;
          }
        }
        .rateTime {
          position: absolute;
          top: 0;
          right: 0;
          line-height: 12px;
          // margin-right: 18px;
          font-size: 10px;
          color: rgb(147, 153, 159);
        }
      }
    }
  }
}
</style>
