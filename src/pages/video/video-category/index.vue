<template>
  <view class="video_category">
    <navigator
      class="cate_item"
      v-for="item in category"
      :key="item.id"
      :url="`/pages/videoCategory/index?id=${item.id}`"
    >
      <image mode="aspectFill" :src="item.cover" />
      <view class="cate_name">{{item.name}}</view>
    </navigator>
  </view>
</template> 

<script>
export default {
  props: {
    urlobj: Object,
  },
  data() {
    return {
      category: [],
    };
  },
  mounted() {
    this.getList();
  },
  methods: {
    // 获取数据
    getList() {
      this.request({
        url: this.urlobj.url,
      }).then((result) => {
        // console.log(result);
        this.category = result.res.category;
      });
    },
  },
};
</script>

<style lang="scss" scoped>
.video_category {
  display: flex;
  flex-wrap: wrap;
  .cate_item {
    width: 50%;
    position: relative;
    border: 5rpx solid #fff;
    image {
      height: 320rpx;
    }

    .cate_name {
      position: absolute;
      width: 100%;
      height: 50rpx;
      left: 0;
      bottom: 0;
      color: #fff;
      // css3 渐变
      background-image: linear-gradient(
        to right top,
        rgba(0, 0, 0, 0.2),
        rgba(0, 0, 0, 0)
      );
      font-size: 40rpx;
      display: flex;
      align-items: center;
      padding-left: 20rpx;
    }
  }
}
</style>