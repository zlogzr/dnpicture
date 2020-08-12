<template>
  <scroll-view class="album_scroll-view" scroll-y @scrolltolower="handleToLower">
    <!-- 轮播图 开始 -->
    <view class="album_swiper">
      <swiper class="swiper" indicator-dots autoplay circular>
        <swiper-item v-for="item in banner" :key="item.id">
          <image :src="item.thumb" />
        </swiper-item>
      </swiper>
    </view>
    <!-- 轮播图 结束 -->

    <!-- 列表 开始 -->
    <view class="album_list">
      <navigator
        class="album_item"
        :url="`/pages/album/index?id=${item.id}`"
        v-for="item in album"
        :key="item.id"
      >
        <view class="album_img">
          <image mode="aspectFill" :src="item.cover" />
        </view>
        <view class="album_info">
          <view class="album_name">{{item.name}}</view>
          <view class="album_desc">{{item.desc}}</view>
          <view class="album_btn">
            <view class="album_attention">关注</view>
          </view>
        </view>
      </navigator>
    </view>
    <!-- 列表 结束 -->
  </scroll-view>
</template>

<script>
export default {
  data() {
    return {
      params: {
        limit: 30,
        order: "new",
        skip: 0,
      },
      // 轮播图数组
      banner: [],
      // 列表数据组
      album: [],
      // 是否还有分页数据
      hasMore: true,
    };
  },
  mounted() {
    // 修改页面标题
    uni.setNavigationBarTitle({
      title: "专辑",
    });
    //获取接口的数据
    this.getList();
  },
  methods: {
    // 获取接口数据
    getList() {
      this.request({
        url: "http://157.122.54.189:9088/image/v1/wallpaper/album",
        data: this.params,
      }).then((result) => {
        // console.log(result);
        // 判断还有没有下一页数据
        if (result.res.album.length === 0) {
          uni.showToast({
            title: "没有更多数据了",
            icon: "none",
          });
          this.hasMore = false;
          return;
        }

        // 只请求一次的数据
        if (this.banner.length === 0) {
          // 轮播图数据
          this.banner = result.res.banner;
        }
        // 列表数据
        this.album = [...this.album, ...result.res.album];
      });
    },
    // 触底请求
    handleToLower() {
      if (this.hasMore) {
        this.params.skip += this.params.limit;
        this.getList();
      } else {
        // 弹窗提示用户
        uni.showToast({
          title: "没有数据了",
          icon: "none",
          duration: 2000,
        });
      }
    },
  },
};
</script>

<style lang="scss" scoped>
.album_scroll-view {
  height: calc(100vh - 36px);
}
// 轮播图
.album_swiper {
  swiper {
    height: calc(750rpx / 2.3);
    image {
      height: 100%;
    }
  }
}
// 列表
.album_list {
  padding: 10rpx;
  .album_item {
    display: flex;
    padding: 10rpx 0;
    border-bottom: 1rpx solid #ccc;
    .album_img {
      flex: 1;
      padding: 10rpx;
      image {
        width: 200rpx;
        height: 200rpx;
      }
    }

    .album_info {
      flex: 2;
      padding: 10rpx;
      overflow: hidden;
      .album_name {
        padding-bottom: 10rpx;
        font-size: 30rpx;
        color: #000;
      }

      .album_desc {
        padding: 10rpx 0;
        font-size: 24rpx;

        text-overflow: ellipsis;
        overflow: hidden;
        white-space: nowrap;
      }

      .album_btn {
        padding: 20rpx;
        display: flex;
        justify-content: flex-end;
        .album_attention {
          color: $color;
          border: 1rpx solid $color;
          padding: 10rpx;
        }
      }
    }
  }
}
</style>