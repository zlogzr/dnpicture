<template>
  <scroll-view
    scroll-y
    @scrolltolower="handleToLower"
    class="scroll_view"
    v-if="recommends.length>0"
  >
    <!-- :url="`/pages/album/index?id=${item.target}`" -->
    <!-- 推荐 开始 -->
    <view class="recommend_wrap">
      <navigator
        url="/pages/album/index?id=5e5cf679e7bce739db1281e4"
        class="recommend_item"
        v-for="item in recommends"
        :key="item.id"
      >
        <image :src="item.thumb" mode="widthFix" />
      </navigator>
    </view>
    <!-- 推荐 结束 -->
    <!-- 月份 开始 -->
    <view class="monthes_wrap">
      <view class="monthes_title">
        <view class="monthes_title_info">
          <view class="monthes_info">
            <text>{{monthes.DD}} /</text>
            {{monthes.MM}} 月
          </view>
          <view class="monthes_text">{{monthes.title}}</view>
        </view>
        <view class="monthes_title_more">更多></view>
      </view>
      <view class="monthes_content">
        <view class="monthes_item" v-for="(item,index) in monthes.items" :key="item.id">
          <go-detail :list="monthes.items" :index="index">
            <image mode="aspectFill" :src="item.thumb+item.rule.replace('$<Height>',360)" />
          </go-detail>
        </view>
      </view>
    </view>
    <!-- 月份 结束 -->
    <!-- 热门 开始 -->
    <view class="hots_wrap">
      <view class="hots_title">
        <text>热门</text>
      </view>
      <view class="hots_content">
        <view class="hots_item" v-for="(item,index) in hots" :key="item.id">
          <go-detail :list="hots" :index="index">
            <image :src="item.thumb" mode="widthFix" />
          </go-detail>
        </view>
      </view>
    </view>
    <!-- 热门 结束 -->
  </scroll-view>
</template>

<script>
import moment from "moment";
import goDetail from "@/components/goDetail";
export default {
  components: {
    goDetail,
  },
  data() {
    return {
      // 推荐
      recommends: [],
      // 月份
      monthes: {},
      // 热门
      hots: [],
      // 请求的参数
      params: {
        // 要获取几条
        limit: 30,
        // 关键字
        order: "hot",
        // 要跳过几条
        skip: 0,
      },
      hasMore: true,
    };
  },
  mounted() {
    // 修改页面标题
    uni.setNavigationBarTitle({
      title: "推荐",
    });
    this.getList();
  },
  methods: {
    // 滑动条触底事件
    handleToLower() {
      if (this.hasMore) {
        this.params.skip += this.params.limit;
        this.getList();
      } else {
        // 弹窗提示用户
        uni.showToast({
          title: "没有数据了",
          duration: 2000,
          icon: "none",
        });
      }
    },
    // 获取接口的数据
    getList() {
      this.request({
        url: "http://157.122.54.189:9088/image/v3/homepage/vertical",
        data: this.params,
      }).then((result) => {
        // console.log(result);

        // 判断还有没有下一页数据
        if (result.res.vertical.length === 0) {
          uni.showToast({
            title: "没有更多数据了",
            icon: "none",
          });
          this.hasMore = false;
          return;
        }

        // 只请求一次的数据
        if (this.recommends.length === 0) {
          // 推荐数据
          this.recommends = result.res.homepage[1].items;
          // 月份数据
          this.monthes = result.res.homepage[2];
          // 修改时间戳
          this.monthes.MM = moment(this.monthes.stime).format("MM");
          this.monthes.DD = moment(this.monthes.stime).format("DD");
        }
        // 热门数据
        this.hots = [...this.hots, ...result.res.vertical];
      });
    },
  },
};
</script>

<style lang="scss" scoped>
.scroll_view {
  height: calc(100vh - 36px);
}
// 推荐
.recommend_wrap {
  display: flex;
  flex-wrap: wrap;

  .recommend_item {
    width: 50%;
    border: 5rpx solid #fff;
  }
}
// 月份
.monthes_wrap {
  .monthes_title {
    display: flex;
    justify-content: space-between;
    padding: 20rpx;
    .monthes_title_info {
      display: flex;
      font-weight: 600;
      .monthes_info {
        color: $color;
        font-size: 30rpx;
        text {
          font-size: 36rpx;
        }
      }

      .monthes_text {
        font-size: 34rpx;
        color: #666;
        margin-left: 25rpx;
      }
    }

    .monthes_title_more {
      font-size: 26rpx;
      color: $color;
    }
  }

  .monthes_content {
    display: flex;
    flex-wrap: wrap;
    .monthes_item {
      width: 33.33%;
      border: 5rpx solid #fff;
    }
  }
}
// 热门
.hots_wrap {
  .hots_title {
    padding: 20rpx;
    text {
      border-left: 15rpx solid $color;
      padding-left: 20rpx;
      font-size: 34rpx;
      font-weight: 600;
    }
  }

  .hots_content {
    display: flex;
    flex-wrap: wrap;
    .hots_item {
      width: 33.33%;
      border: 5rpx solid #fff;
      image {
      }
    }
  }
}
</style>