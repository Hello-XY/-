<template>
  <view>
    <van-card :thumb-link='`/subpkg/good_detail/good_detail?id=${item.goods_id}`'
      :thumb="item.goods_small_logo||defaultPic" v-for="item in goods" :key="item.goods_id"
      :price="item.goods_price|toFixed" :title="item.goods_name" />
  </view>
</template>

<script>
  import {
    getSearchGoods
  } from '@/api/good.js'
  import toast from '@/utils/totas.js'
  export default {
    data() {
      return {
        // 请求参数对象
        queryObj: {
          // 查询关键词
          query: '',
          // 商品分类Id
          cid: '',
          // 页码值
          pagenum: 1,
          // 每页显示多少条数据
          pagesize: 10
        },
        goods: [],
        total: 0,
        // 默认的空图片
        defaultPic: 'https://img3.doubanio.com/f/movie/8dd0c794499fe925ae2ae89ee30cd225750457b4/pics/movie/celebrity-default-medium.png'
      }
    },
    onLoad(options) {
      console.log(options);
      this.queryObj.query = options.query || ''
      this.queryObj.cid = options.cid || ''
      this.onloadGoods()
    },
    methods: {
      //获取商品列表
      async onloadGoods(stopPullDownRefresh) {
        this.isloading = true
        const {
          goods,
          total
        } = await getSearchGoods(this.queryObj)
        this.goods = [...this.goods, ...goods]
        this.total = total
        stopPullDownRefresh && stopPullDownRefresh()
        this.isloading = false
      },
      onPullDownRefresh() {
        this.queryObj = {
          // 查询关键词
          query: '',
          // 商品分类Id
          cid: '',
          // 页码值
          pagenum: 1,
          // 每页显示多少条数据
          pagesize: 10
        }
        this.goods = []
        this.total = 0
        this.onloadGoods(() => {
          uni.stopPullDownRefresh()
        })
      },
      //监听上拉触底
      onReachBottom() {
        if (this.queryObj.pagenum * this.queryObj.pagesize >= this.total) return toast('数据加载完毕！')
        if (this.isloading) return
        this.queryObj.pagenum += 1
        this.onloadGoods()
      }
    }
  }
</script>

<style lang="scss">

</style>
