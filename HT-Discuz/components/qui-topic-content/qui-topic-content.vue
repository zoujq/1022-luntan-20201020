<template>
  <view v-if="topicStatus != 1">
    <view class="themeItem__header">
      <view class="themeItem__header__img" @click="personJump">
        <qui-avatar :user="{ username: userName, avatarUrl: avatarUrl }" :is-real="isReal" />
      </view>
      <view class="themeItem__header__title">
        <view class="themeItem__header__title__top" @click="personJump">
          <text class="themeItem__header__title__username">{{ userName }}</text>
          <text
            class="themeItem__header__title__isAdmin"
            v-for="(group, index) in userRole"
            :key="index"
            :class="group.isDisplay ? 'themeItem__header__title__isAdminColor' : ''"
          >
            {{ group.isDisplay ? `${group.name}` : '' }}
          </text>
        </view>
        <view class="themeItem__header__title__time">{{ localTime }}</view>
      </view>
      <view class="themeItem__header__opera">
        <image
          v-if="threadPrice > 0"
          src="@/static/payment.png"
          class="essence"
          :class="threadIsEssence ? 'marginLf' : ''"
        ></image>
        <image v-if="threadIsEssence" src="@/static/essence.png" class="essence"></image>
        <slot name="more"></slot>
      </view>
    </view>
  </view>
  <view class="themeItem" v-else>
    <view class="themeItem__header">
      <view class="themeItem__header__img" @click="personJump">
        <qui-avatar :user="{ username: userName, avatarUrl: avatarUrl }" :is-real="isReal" />
      </view>
      <view class="themeItem__header__title">
        <view class="themeItem__header__title__top" @click="personJump">
          <!--<text
            class="themeItem__header__title__username"
            v-if="followShow && !managementShow"
            :style="{ maxWidth: '200rpx' }"
          >
            {{ userName }}
          </text>
          <text
            class="themeItem__header__title__username"
            v-else-if="followShow && managementShow"
            :style="{ maxWidth: '90rpx' }"
          >
            {{ userName }}
          </text>
          <text class="themeItem__header__title__username" v-else :style="{ maxWidth: '370rpx' }">
            {{ userName }}
          </text>-->
          <text class="themeItem__header__title__username">
            {{ userName }}
          </text>
          <text
            class="themeItem__header__title__isAdmin"
            v-for="(group, index) in userRole"
            :key="index"
            :class="group.isDisplay ? 'themeItem__header__title__isAdminColor' : ''"
          >
            {{ group.isDisplay ? `${group.name}` : '' }}
          </text>
        </view>
        <view class="themeItem__header__title__time">{{ localTime }}</view>
      </view>
      <slot name="follow"></slot>
      <view class="themeItem__header__opera">
        <image
          v-if="threadPrice > 0"
          src="@/static/payment.png"
          class="essence"
          :class="threadIsEssence ? 'marginLf' : ''"
        ></image>
        <image v-if="threadIsEssence" src="@/static/essence.png" class="essence"></image>
        <slot name="more"></slot>
      </view>
    </view>

    <view class="themeItem__content">
      <view
        class="themeItem__content__con"
        :style="{ position: payStatus ? 'static' : 'relative' }"
      >
        <view class="themeItem__content__con__title" v-if="themeType == 1 && themeTitle">
          {{ themeTitle }}
        </view>
        <view class="themeItem__content__text" v-if="themeContent">
          <qui-uparse :content="themeContent"></qui-uparse>
        </view>
        <view
          class="theme__content__videocover"
          v-if="themeType == 2 && !videoStatus && coverImage != null"
          @click="videocoverClick"
          :style="{ width: '100%', height: videoWidth > videoHeight ? '' : '860rpx' }"
          :mode="videoWidth > videoHeight ? 'widthFix' : 'aspectFill'"
        >
          <view class="theme__mark"></view>
          <image class="theme__mark__open" src="/static/video.svg"></image>
          <image
            class="themeItem__content__coverimg"
            :src="coverImage"
            :style="{ height: videoWidth > videoHeight ? '' : '100%' }"
          ></image>
        </view>
        <view
          class="theme__content__videocover"
          @click="btnFun"
          v-if="themeType == 2 && videoStatus"
          :style="{ display: sun, height: videoWidth > videoHeight ? '' : '860rpx' }"
        >
          <image class="theme__mark__open" src="/static/video.svg"></image>
          <image
            class="themeItem__content__coverimg"
            :src="coverImage"
            :style="{ height: videoWidth > videoHeight ? '' : '100%' }"
            :mode="videoWidth > videoHeight ? 'widthFix' : 'aspectFill'"
          ></image>
        </view>
        <view v-show="videoShow">
          <video
            ref="myVideo"
            id="myVideo"
            :poster="coverImage"
            :autoplay="autoplay"
            controls
            :duration="duration"
            preload="none"
            bindpause="handlepause"
            playsinline
            webkit-playsinline
            x5-playsinline
            :page-gesture="false"
            :show-fullscreen-btn="true"
            :show-play-btn="true"
            auto-pause-if-open-native
            auto-pause-if-navigate
            :enable-play-gesture="false"
            :vslide-gesture="false"
            :vslide-gesture-in-fullscreen="false"
            object-fit="contain"
            :direction="videoWidth > videoHeight ? 90 : 0"
            x5-video-player-type="h5-page"
            bindfullscreenchange="fullScreen"
            :src="mediaUrl"
            :style="{
              width: '100%',
              height: videoWidth > videoHeight ? blocKwidth + 'rpx' : '860rpx',
            }"
          ></video>
        </view>
        <qui-image
          :images-list="imagesList"
          :preview-status="videoStatus"
          @previewPicture="previewPicture"
        ></qui-image>
        <view
          v-if="!payStatus && threadPrice > 0 && themeType == 1"
          class="themeItem__content__con__cover"
          :style="{
            background:
              theme === $u.light()
                ? 'linear-gradient(rgba(255, 255, 255, 0), rgba(255, 255, 255, 1))'
                : 'linear-gradient(rgba(0, 0, 0, 0), rgba(0, 0, 0, 1))',
          }"
        ></view>
        <view
          v-if="!payStatus && threadPrice > 0 && themeType == 1"
          class="themeItem__content__con__surtip"
        >
          {{ p.surplus }}{{ p.contentHide }}
        </view>
      </view>
      <!-- 附件 -->
      <view class="themeItem__content__attachment" v-if="fileList.length > 0">
        <view class="themeItem__content__attachment-title">
          {{ i18n.t('profile.attachment') }}
        </view>
        <view
          class="themeItem__content__attachment-item"
          v-for="(item, index) in attachMentList"
          :key="index"
        >
          <view
            v-if="['MP3', 'M4A', 'WAV', 'AAC'].indexOf(item.format) !== -1"
            class="themeItem__content__attachment-item-wrap"
          >
            <qui-audio
              :src="item.url"
              :name="item.fileName"
              :audio-id="item._jv.id"
              :ref="'audio' + item._jv.id"
              @audioPlay="audioPlay"
            ></qui-audio>
          </view>
          <view @tap="download(item)" v-else class="attachment-name">
            <qui-icon
              class="icon-attachment"
              :name="item.fileName ? `icon-${item.format}` : `icon-resources`"
              color="#aaa"
              size="22"
            ></qui-icon>
            <text>{{ item.fileName }}</text>
          </view>
        </view>
      </view>

      <view class="themeItem__content__tags" v-if="tags.length > 0">
        <view
          class="themeItem__content__tags__item"
          v-for="(tag, index) in tags"
          :key="index"
          @click="tagClick(tag._jv.id)"
        >
          {{ tag.name }}
        </view>
      </view>
      <view
        class="themeItem__content__tags  themeItem__content__tags--position"
        v-if="threadPosition.length > 0"
      >
        <view class="themeItem__content__tags__item" @tap="topicPosition">
          <qui-icon name="icon-weizhi" size="30" color="#777"></qui-icon>
          <text class="themeItem__content__tags__item-text">
            {{ threadPosition.length > 0 && threadPosition[0] }}
          </text>
        </view>
      </view>
    </view>
    <qui-toast ref="toast"></qui-toast>
  </view>
</template>

<script>
import { time2DateAndHM } from '@/utils/time';
import { status } from '@/library/jsonapi-vuex/index';
import { setCookie } from '@/utils/setCookie';

export default {
  props: {
    topicStatus: {
      type: Number,
      default: 0,
    },
    // 类型
    themeParts: {
      validator: value => {
        // 0 主题  1 回复
        return ['0', '1'].indexOf(value) !== -1;
      },
      default: '0',
    },
    followShow: {
      type: Boolean,
      default: false,
    },
    // 主题类型
    // themeType: {
    //   validator: value => {
    //     // 0 文字  1 帖子  2 视频 3 图片
    //     return ['0', '1', '2', '3'].indexOf(value) !== -1;
    //   },
    //   default: '1',
    // },、
    // 主题类型 0 文字  1 帖子  2 视频 3 图片
    themeType: {
      type: Number,
      default: 0,
    },
    // 是否支付
    payStatus: {
      type: Boolean,
      default: true,
    },
    // 视频显示状态
    videoStatus: {
      type: Boolean,
      default: true,
    },
    // 当前主题价格
    threadPrice: {
      type: [Number, String],
      default: 0,
    },
    userInfo: {
      type: Object,
      default: () => {
        return {};
      },
    },
    // 需要支付查看的内容所占的比例
    // partVal: {
    //   type: [Number, String],
    //   default: 0,
    // },
    // 主题的用户的角色
    userRole: {
      type: Array,
      default: () => {
        return [];
      },
    },
    userId: {
      type: [Number, String],
      default: '',
    },
    // 发布主题的用户头像
    avatarUrl: {
      type: String,
      default: '',
    },
    // 发布主题的用户名
    userName: {
      type: String,
      default: '',
    },
    // 实名认证
    isReal: {
      type: Boolean,
      default: false,
    },
    // 管理菜单
    selectList: {
      type: Array,
      default: () => {
        return [];
      },
    },
    // 主题标题（当类型是帖子（1）时）
    themeTitle: {
      type: String,
      default: '',
    },
    // 是否加精
    threadIsEssence: {
      type: Boolean,
      default: false,
    },
    // 发布内容
    themeContent: {
      type: String,
      default: '',
    },
    // 发布时间
    themeTime: {
      type: String,
      default: '',
    },
    managementShow: {
      type: Boolean,
      default: false,
    },
    // 主题图片
    imagesList: {
      type: Array,
      default: () => {
        return [];
      },
    },
    // // 图片裁剪、缩放的模式
    // modeVal: {
    //   type: String,
    //   default: 'aspectFill',
    // },
    // 视频宽度
    videoWidth: {
      type: Number,
      default: 0,
    },
    // 视频高度
    videoHeight: {
      type: Number,
      default: 0,
    },
    // 主题id
    themid: {
      type: [Number, String],
      default: 0,
    },
    // 主题相关标签
    tags: {
      type: Array,
      default: () => {
        return [];
      },
    },
    coverImage: {
      type: String,
      default: '',
    },
    duration: {
      type: String,
      default: '',
    },
    mediaUrl: {
      type: String,
      default: '',
    },
    fileList: {
      type: Array,
      default: () => {
        return [];
      },
    },
    threadPosition: {
      type: Array,
      default: () => {
        return [];
      },
    },
  },
  data: () => {
    return {
      seleShow: false, // 默认收起管理菜单
      selectActive: false,
      imageStatus: true, // 头像地址错误时显示默认头像
      // topicStatus: '',
      videoShow: false,
      attachMentList: [],
      autoplay: false,
      look: true,
      sun: 1,
      blocKwidth: 224,
    };
  },
  computed: {
    t() {
      return this.i18n.t('topic');
    },
    p() {
      return this.i18n.t('pay');
    },
    // 时间转化
    localTime() {
      return time2DateAndHM(this.themeTime);
    },
  },
  mounted() {
    const { fileList } = this;
    fileList.forEach((e, index) => {
      fileList[index].format = e.fileName.substring(e.fileName.lastIndexOf('.') + 1).toUpperCase();
    });
    this.attachMentList = fileList;
    this.blocKwidth = (660 / this.videoWidth) * this.videoHeight;
  },
  methods: {
    // 管理菜单点击事件
    selectClick() {
      this.seleShow = !this.seleShow;
      // this.selectActive = this.seleShow ? '#1878F3' : '#333333';
    },
    // 管理菜单选中事件
    selectChoice(param) {
      this.$emit('selectChoice', param);
      this.seleShow = false;
      // this.selectActive = this.seleShow ? '#1878F3' : '#333333';
    },
    // 点击用户头像以及用户名事件
    personJump() {
      this.$emit('personJump', this.userId);
    },
    // 点击视频封面图事件
    videocoverClick() {
      this.$emit('videocoverClick');
      console.log('点击封面图事件');
    },

    // 点击分类标签
    tagClick(tagId) {
      this.$emit('tagClick', tagId);
    },
    // 头像失效
    imageError() {
      this.imageStatus = false;
    },
    // 附件下载
    download(item) {
      // #ifdef H5
      const { platform } = uni.getSystemInfoSync();
      if (platform === 'ios') {
        this.$refs.toast.show({
          message: this.i18n.t('profile.filedownloadtips'),
        });
      } else {
        const token = uni.getStorageSync('access_token');
        setCookie('token', token, 30);
        window.location.href = item.url;
      }
      // #endif
      // #ifdef MP-WEIXIN
      const that = this;
      wx.downloadFile({
        url: item.url,
        success(res) {
          if (res.statusCode === 200) {
            that.$refs.toast.show({
              message: that.i18n.t('profile.downloadSuccess'),
            });
            wx.openDocument({
              filePath: res.tempFilePath,
              success(data) {
                console.log(data);
              },
            });
          }
        },
        error() {
          that.$refs.toast.show({
            message: that.i18n.t('profile.downloadError'),
          });
        },
      });
      // this.$refs.toast.show({
      //   message: this.i18n.t('profile.filedownloadtipswx'),
      // });
      // #endif
    },
    // 只能播放一个音频
    audioPlay(id) {
      const { attachMentList } = this;
      const that = this;
      attachMentList.forEach(item => {
        if (id !== item._jv.id && ['MP3', 'M4A', 'WAV', 'AAC'].indexOf(item.format) !== -1) {
          that.$refs[`audio${item._jv.id}`][0].audioPause();
        }
      });
    },
    // 地理位置
    topicPosition() {
      const { threadPosition } = this;
      uni.navigateTo({
        url: `/pages/topic/position?longitude=${threadPosition[2]}&latitude=${threadPosition[3]}`,
      });
    },
    previewPicture() {
      this.$emit('previewPicture');
    },
    serBtn() {
      const params = {
        include: [
          'posts.replyUser',
          'user.groups',
          'user',
          'posts',
          'posts.user',
          'posts.likedUsers',
          'posts.images',
          'firstPost',
          'firstPost.likedUsers',
          'firstPost.images',
          'firstPost.attachments',
          'rewardedUsers',
          'category',
          'threadVideo',
          'paidUsers',
          'user.groups.permissionWithoutCategories',
        ],
      };
      const threadAction = status.run(() =>
        this.$store.dispatch('jv/get', [`threads/${this.themid}`, { params }]),
      );
      threadAction.then(() => {
        this.sun = 'none';
        this.videoShow = true;
        this.autoplay = true;
        setTimeout(() => {
          console.log('视频开始播放');
          const videoContext = wx.createVideoContext('myVideo');
          videoContext.play();
        }, 200);
      });
    },

    btnFun() {
      this.serBtn();
    },
  },
};
</script>

<style lang="scss" scoped>
@import '@/styles/base/variable/global.scss';
@import '@/styles/base/theme/fn.scss';
.themeItem {
  width: 100%;
  background: --color(--qui-BG-2);

  &__header {
    display: flex;
    flex-direction: row;
    justify-content: flex-start;
    width: 100%;
    padding: 30rpx 40rpx 37rpx;
    box-sizing: border-box;
    &__img {
      width: 80rpx;
      height: 80rpx;
      // margin-right: 18rpx;
      background: #ccc;
      border-radius: 100%;
      image {
        width: 100%;
        height: 100%;
        border-radius: 100%;
      }
    }

    &__title {
      flex: 1;
      margin-left: 18rpx;
      &__top {
        display: flex;
        flex-direction: row;
        justify-content: flex-start;
        height: 37rpx;
        margin-bottom: 10rpx;
        margin-left: 2rpx;
        font-size: $fg-f4;
        line-height: 37rpx;
      }

      &__username {
        display: flex;
        height: 37rpx;
        max-width: 326rpx;
        overflow: hidden;
        font-weight: bold;
        line-height: 37rpx;
        color: --color(--qui-FC-000);
        text-overflow: ellipsis;
        white-space: nowrap;
      }

      &__isAdmin {
        font-weight: 400;
        color: --color(--qui-FC-AAA);
        white-space: nowrap;
      }

      &__isAdminColor {
        padding: 2rpx 10rpx;
        margin-left: 15rpx;
        font-size: $fg-f1;
        background: --color(--qui-BG-IT);
        border-radius: 18rpx;
        box-sizing: border-box;
      }

      &__time {
        font-size: $fg-f2;
        font-weight: 400;
        line-height: 31rpx;
        color: --color(--qui-FC-AAA);
      }
    }
    &__opera {
      align-self: flex-start;
      width: 100rpx;
      margin-left: 29rpx;
      text-align: right;
      flex-shrink: 0;

      .marginLf {
        margin-right: 6rpx;
      }

      .essence {
        display: inline-block;
        width: 35rpx;
        height: 45rpx;
        margin-bottom: 10rpx;
        vertical-align: top;
      }
    }
  }

  &__content {
    &__title {
      word-break: break-all;
    }
    &__coverimg {
      width: 100%;
    }
    &__con {
      padding-bottom: 20rpx;
      &__title {
        padding-bottom: 40rpx;
        font-size: 30rpx;
        font-weight: 600;
        line-height: 160%;
        text-align: left;
        word-break: break-all;
      }
      &__cover {
        position: absolute;
        right: 0;
        bottom: 0;
        left: 0;
        height: 240rpx;
        // background: linear-gradient(rgba(255, 255, 255, 0), rgba(255, 255, 255, 1));
      }
      &__surtip {
        position: relative;
        z-index: 6;
        padding-top: 37rpx;
        padding-bottom: 20rpx;
        font-size: $fg-f4;
        line-height: 37rpx;
        text-align: center;
      }
    }

    padding: 0 40rpx;
    &__text {
      margin-bottom: 12rpx;
      overflow: hidden;
      font-size: $fg-f4;
      font-weight: 400;
      line-height: 160%;
      word-break: break-all;
      img {
        display: inline-block;
        width: 28rpx;
        height: 28rpx;
      }
    }

    &__tags {
      display: flex;
      flex-wrap: wrap;
      margin-bottom: 76rpx;

      &__item {
        height: 50rpx;
        padding: 0 20rpx;
        margin-right: 10rpx;
        margin-bottom: 8rpx;
        font-family: $font-family;
        font-size: 24rpx;
        font-weight: 400;
        line-height: 50rpx;
        color: --color(--qui-FC-TAG);
        text-align: center;
        background: --color(--qui-BG-F7);
        border-radius: 6rpx;
        transition: $switch-theme-time;
      }
    }
    &__tags--position {
      margin-top: -60rpx;
    }
    &__tags__item-text {
      margin-left: 10rpx;
    }
    &__attachment {
      margin-top: 40rpx;
      margin-bottom: 20rpx;
      &-title {
        margin-bottom: 20rpx;
        font-size: 24rpx;
        font-weight: bold;
        color: --color(--qui-FC-777);
      }
      &-item {
        height: 60rpx;
        padding: 0 20rpx;
        margin-bottom: 10rpx;
        overflow: hidden;
        font-size: 24rpx;
        line-height: 60rpx;
        border: 2rpx solid --color(--qui-BOR-ED);
        border-radius: 5rpx;
        box-sizing: border-box;
      }
      &-item-wrap {
        width: 100%;
      }
      .icon-attachment {
        margin-right: 10rpx;
      }
    }
  }

  &__footer {
    &__themeType1 {
      display: flex;
      justify-content: space-between;
      margin-top: 60rpx;

      &__item {
        font-family: $font-family;
        font-size: 28rpx;
        font-weight: 400;
        line-height: 37rpx;
        color: rgba(170, 170, 170, 1);
      }

      text {
        margin-right: 15rpx;
      }

      &__greated {
        color: rgba(221, 221, 221, 1);
      }
    }

    &__themeType2 {
      &__item {
        font-family: $font-family;
        font-size: $fg-f4;
        font-weight: 400;
        line-height: 37rpx;
        color: rgba(170, 170, 170, 1);
        text-align: right;
      }
      text {
        margin-right: 15rpx;
      }
    }
  }
}
.attachment-name {
  max-width: 100%;
  overflow: hidden;
  font-size: $fg-f2;
  text-overflow: ellipsis;
  white-space: nowrap;
}
.det-hd-operaCli {
  position: relative;
  z-index: 10;
  font-size: $fg-f4;
  line-height: 40rpx;
  .det-hd-management {
    display: flex;
    flex-direction: row;
    justify-content: flex-end;
    font-size: $fg-f4;
    line-height: 1;
    .icon-management {
      margin-right: 7rpx;
      font-size: $fg-f3;
    }
  }
}
.theme__mark {
  position: absolute;
  z-index: 1;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.2);
  opacity: 0;
}
.theme__mark__open {
  position: absolute;
  top: 50%;
  left: 50%;
  z-index: 2;
  width: 80rpx;
  height: 80rpx;
  margin-top: -40rpx;
  margin-left: -40rpx;
}
.theme__content__videocover {
  position: relative;
}
.cont-box {
  width: 600rpx;
  height: 400rpx;
  background: brown;
}
</style>
