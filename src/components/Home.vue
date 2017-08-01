<template lang='pug'>
  div#home
    div.header
      a.top-bar-download(href="https://app.toutiao.com/news_article/", target="_blank") 下载APP
      div.weather
        span.weather-cell 北京
        span.weather-cell 多云
        span.weather-cell 23°&nbsp;&nbsp;/&nbsp;&nbsp;33°
        img(src="../assets/arrow-down.png")
      div.other
        a.log-in(href="https://sso.toutiao.com/login/") 登录
        a.other-cell 反馈
        a.other-cell 侵权投诉
        a.other-cell 头条产品
    //- 主要内容
    div.container
      //- 侧边栏
      div.side-bar
        img.logo(src="../assets/logo.png")
        ul.channel-ul
          li.channel-li.active 推荐
          li.channel-li 热点
          li.channel-li 视频
          li.channel-li 图片
          li.channel-li 段子
          li.channel-li 社会
          li.channel-li 娱乐
          li.channel-li 科技
          li.channel-li 体育
          li.channel-li 汽车
          li.channel-li 财经
          li.channel-li 搞笑
          li.channel-li 更多
      //- 中间部分
      div.content-wrap
        div.swiper-wrap
          div.swiper-img-wrap
            div.swiper-img-item(v-for='item in swiper', @click='imgClick(item.display_url)')
              img(:src='item.image_url')
              p.switer-item-title {{ item.title }}
          div.swiper-item-wrap
            span.active(@mouseover='showImg(0)') 要闻
            span(@mouseover='showImg(1)') 社会
            span(@mouseover='showImg(2)') 娱乐
            span(@mouseover='showImg(3)') 体育
            span(@mouseover='showImg(4)') 军事
            span(@mouseover='showImg(5)') 明星
        div.news-wrap
          div.news-item(v-for='item in newsList', :class='item.single_mode ? "has-image" : "no-image"')
            img(v-if='item.single_mode', :src='item.image_url')
            div.news-content
              div.news-content-inner
                a.news-title(:href='"https://www.toutiao.com/" + item.source_url', target='_blank') {{ item.title }}
                div.news-meta
                  a.news-tag.video(v-if='item.chinese_tag') {{ item.chinese_tag }}
                  a.news-avatar
                    img(:src='item.media_avatar_url')
                  a.news-source {{ item.source }} ⋅
                  a.news-comment  {{ item.comments_count }}评论 ⋅
                  a.news-time  55分钟前
          p.loading 加载中...


</template>

<script>
  import axios from 'axios'

  export default {
    name: 'home',
    data () {
      return {
        swiper: [],
        nextQuery: {},
        newsList: []
      }
    },

    created () {
      let Vue = this
      // 获取轮播图
      axios.get('/api/focus/')
        .then((res) => {
          let data = res.data.data.pc_feed_focus
          Vue.swiper = data
        })

      // 首次请求数据
      axios.get('/api/feed/?min_behot_time=0&category=__all__&utm_source=toutiao&widen=1&tadrequire=true&as=A12519F7C000D95&cp=5970509DC9458E1')
        .then((res) => {
          Vue.nextQuery = res.data.next
          Vue.newsList = res.data.data

          Vue.$nextTick(() => {
            // 滚动加载更多
            window.addEventListener('scroll', Vue.scrollToBottom)
          })
        })
    },

    methods: {
      // 轮播图效果
      showImg (index) {
        let imgCollection = document.getElementsByClassName('swiper-img-wrap')[0].getElementsByTagName('img')
        Array.prototype.forEach.call(imgCollection, (item, index) => {
          item.style.display = 'none'
        })
        imgCollection[index].style.display = 'block'

        // 列表 hover 效果
        let itemCollection = document.getElementsByClassName('swiper-item-wrap')[0].getElementsByTagName('span')
        Array.prototype.forEach.call(itemCollection, (item, index) => {
          item.className = ''
        })
        itemCollection[index].className = 'active'
      },

      // 点击轮播图图片进入详情页
      imgClick (url) {
        window.open(`https://www.toutiao.com/${url}`)
      },

      // 滚动判断
      scrollToBottom () {
        let Vue = this
        let totalHeight = document.documentElement.offsetHeight
        let windowHeight = document.documentElement.clientHeight
        let bodyScrollTop = document.body.scrollTop
        let scrollOffset = totalHeight - bodyScrollTop - windowHeight
        if (scrollOffset < 100) {
          console.log('请求新数据...')
          Vue.getMoreData()
        }
      },

      // 获取更多数据
      getMoreData () {
        let Vue = this
        window.removeEventListener('scroll', Vue.scrollToBottom)
        // let key = Object.keys(Vue.nextQuery)[0]
        // let query = `/api/feed/?${key}=${Vue.nextQuery[key]}&category=__all__`
        let query = '/api/feed/?min_behot_time=0&category=__all__&utm_source=toutiao&widen=1&tadrequire=true&as=A12519F7C000D95&cp=5970509DC9458E1'
        axios.get(query).then((res) => {
          let data = res.data.data
          Vue.nextQuery = res.data.next
          Vue.newsList = Vue.newsList.concat(data)

          Vue.$nextTick(() => {
            // 滚动加载更多
            window.addEventListener('scroll', Vue.scrollToBottom)
          })
        })
      }
    }
  }
</script>

<style scoped lang='less'>
  // top bar
  .header {
    width: 100%;
    height: 34px;
    background-color: #222;
    color: #fff;
    font-size: 14px;
    line-height: 34px;

    a {
      display: inline-block;
      padding-left: 10px;
      padding-right: 10px;
      color: #fff;
    }

    .top-bar-download {
      border-right: 1px solid #3a3a3a;
    }
    // weather
    .weather {
      padding-left: 10px;
      display: inline-block;

      .weather-cell {
        margin-right: 12px;
      }

      img {
        width: 14px;
        vertical-align: middle;
      }
    }

    // 右侧
    .other {
      display: inline-block;
      float: right;
    }

    .log-in {
      display: inline-block;
      width: 68px;
      height: 100%;
      background: #ed4040;
      text-align: center;
    }

    .other-cell {
      border-right: 1px solid #3a3a3a;
    }
  }

  // container
    .container {
      margin: 0 auto;
      margin-top: 16px;
      width: 1170px;
    }


  // side bar
  .side-bar {
    float: left;

    .logo {
      width: 108px;
      height: 27px;
    }

    .channel-ul {
      width: 110px;
      padding-left: 0;
    }

    .channel-li {
      height: 40px;
      line-height: 40px;
      color: rgba(68, 68, 68, 1);
      text-align: center;
      cursor: pointer;
      -webkit-transition: all .4s ;
         -moz-transition: all .4s ;
          -ms-transition: all .4s ;
           -o-transition: all .4s ;
              transition: all .4s ;
    }

    .channel-li.active {
      background-color: #ed4040;
      border-radius: 4px;
      color: #fff;
    }

    .channel-li:hover {
      background-color: #ed4040; 
      border-radius: 4px;
      color: #fff;
    }
  }

  // 中间部分
  .content-wrap {
    float: left;
    width: 660px;
    margin-left: 30px;

    // 轮播图
    .swiper-wrap {
      width: 100%;
      height: 300px;
      background-color: #f4f5f6;
      overflow: hidden;

      .swiper-img-wrap {
        position: relative;
        float: left;
        width: 600px;
        height: 100%;

        .swiper-img-item {
          position: absolute;
          top: 0;
          left: 0;
          width: 100%;
          cursor: pointer;

          img {
            width: 100%;
          }

          .switer-item-title {
            position: absolute;
            bottom: 10px;
            width: 100%;
            height: 44px;
            line-height: 44px;
            padding-left: 22px;
            font-size: 20px;
            color: #fff;
            font-weight: bold;
            background-image: linear-gradient(-180deg,transparent,rgba(0,0,0,.8));
            box-sizing: border-box;
            white-space: nowrap;
            overflow: hidden;
            text-overflow: ellipsis;
          }
        }
      }

      .swiper-item-wrap {
        float: left;
        width: 60px;
        height: 100%;
        padding: 7px 0;
        background-color: #222;
        font-size: 14px;

        span {
          display: inline-block;
          width: 100%;
          height: 36px;
          line-height: 36px;
          margin-bottom: 14px;
          color: #fff;
          text-align: center;
          cursor: pointer;
        }

        span.active {
          background-color: rgba(237,64,64,.8);
        }

        span:hover {
          background-color: rgba(237,64,64,.8);
        }
      }
    }

    // 新闻列表
    .news-wrap {
      width: 100%;

      .loading {
        text-align: center;
        font-size: 16px;
        color: gray;
        margin-top: 10px;
        margin-bottom: 10px;
      }

      .news-item {
        box-sizing: border-box;
        padding: 10px 0;
        border-bottom: 1px solid #e8e8e8;
        overflow: hidden;
      }

      .has-image {
        height: 123px;

        & > img {
          float: left;
          width: 158px;
          margin-right: 16px;
        }

        .news-content::after {
          content: '';
          display: inline-block;
          vertical-align: middle;
          width: 0;
          height: 100%;
        }

        .news-content {
          float: left;
          width: 480px;
          height: 100%;

          .news-content-inner {
            display: inline-block;
            vertical-align: middle;
          }

          // .news-title {
          //   display: block;
          //   color: #222;
          //   font-size: 20px;
          //   font-weight: bold;
          // }

          // .news-meta {
          //   margin-top: 10px;

          //   .news-tag {
          //     font-size: 12px;
          //     margin-right: 10px;
          //     padding: 1px 2px;
          //     border: 1px solid #eee;
          //   }
          //   .news-tag.video {
          //     border-color: #ff7920!important;
          //     color: #ff7920!important;
          //   }

          //   .news-avatar {
          //     margin-right: 2px;

          //     img {
          //       width: 18px;
          //       vertical-align: middle;
          //     }
          //   }

          //   .news-source,
          //   .news-comment {
          //     font-size: 14px;
          //     color: #777;
          //   }

          //   .news-time {
          //     font-size: 14px;
          //     color: #999;
          //   }
          // }
        }
      }

      .no-image {
        height: 77px;
      }

      .news-title {
        display: block;
        color: #222;
        font-size: 20px;
        font-weight: bold;
      }

      .news-meta {
        margin-top: 10px;

        .news-tag {
          font-size: 12px;
          margin-right: 10px;
          padding: 1px 2px;
          border: 1px solid #eee;
        }
        .news-tag.video {
          border-color: #ff7920!important;
          color: #ff7920!important;
        }

        .news-avatar {
          margin-right: 2px;

          img {
            width: 18px;
            vertical-align: middle;
            border-radius: 50%;
          }
        }

        .news-source,
        .news-comment {
          font-size: 14px;
          color: #777;
        }

        .news-time {
          font-size: 14px;
          color: #999;
        }
      }
    }
  }

</style>









