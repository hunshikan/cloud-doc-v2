<template>
  <div class="page-view">
    <div class="header">
      <a :href="user_info.href" class="user-info">
        <div class="avatar">
          <img :src="user_info.avatar" :class="{is_login_avatar:token}" />
        </div>
        <div class="content">
          <div class="name">{{user_info.name}}</div>
          <div class="info">{{user_info.intro}}</div>
        </div>
        <div class="right" v-if="false">
          <i class="iconfont icon-moreinfo-copy"></i>
        </div>
      </a>
      <div class="grid-list" v-if="token">
        <div class="grid-item" @click="grid_list_click('subscribe-doc%3fv%3dt')">
          <p class="number">{{user.subscribe_doc || 0}}</p>
          <p class="name">收藏</p>
        </div>
        <div v-if="false" class="grid-item" @click="grid_list_click('comment%3fv%3dt')">
          <p class="number">{{user.comment_num || 0}}</p>
          <p class="name">评论</p>
        </div>
        <div class="grid-item" @click="grid_list_click('follow%3fv%3dt')">
          <p class="number">{{user.follow || 0}}</p>
          <p class="name">关注</p>
        </div>
        <div class="grid-item" @click="grid_list_click('fans%3fv%3dt')">
          <p class="number">{{user.fans || 0}}</p>
          <p class="name">粉丝</p>
        </div>
      </div>
    </div>
    <grid-menu :menu="menu"></grid-menu>
  </div>
</template>
<script>
import store from "../login/store";
import { user, http } from "../../../utils";
import { getParam } from "../../../utils/util";
import GridMenu from './grid-menu'
export default {
  components:{
    GridMenu
  },
  data() {
    return {
      page_url: {
        login: "/pages/member/login/main",
        setting: "/pages/member/setting/main",
        created_doc: "/pages/created/doc/main"
      },
      web_host: http.web_host(),
      menu:[]
    };
  },
  computed: {
    user() {
      return store.state.user;
    },
    token() {
      return store.state.token;
    },
    user_info() {
      if (this.token) {
        return {
          is_login: true,
          href: this.page_url.setting,
          avatar: this.user.avatar,
          name: this.user.name,
          intro: this.user.intro || "这家伙很懒，什么也没留下"
        };
      } else {
        return {
          is_login: false,
          href: this.page_url.login,
          avatar: require("../../../../static/images/logo.png"),
          name: "未登录",
          intro: "立即登录，开启您的云档之旅"
        };
      }
    }
  },
  mounted() {
    this.getData();
  },
  methods: {
    getData() {
      http.get("v1/member-index").then(res => {
        this.web_host = res.data.web_host;
        this.menu = res.data.menu;
        user.set_user(res.data.user);
        store.commit("set_user", res.data.user);
      });
    },

    grid_list_click(router) {
      if (user.check_login()) {
        let url = this.web_host + router;
        wx.navigateTo({ url: "/pages/web/main?url=" + url });
      }
    }
  },
  onPullDownRefresh() {
    this.getData();
  }
};
</script>
<style lang="less">
@import "../../../styles/common.less";
@avatar-size: 50px;
.header {
  background: @blue;
  color: white;
  box-shadow: 0 1px 6px @blue;
  //border-radius: 0 0 15px 15px;
  overflow: hidden;
  .user-info {
    display: flex;
    align-items: center;
    padding: 8px 15px;
    .avatar {
      width: @avatar-size;
      height: @avatar-size;
      margin-right: 10px;
      img {
        width: @avatar-size;
        height: @avatar-size;
      }
      .is_login_avatar {
        border-radius: 50%;
      }
    }
    .content {
      flex: 1;
      .name {
        font-size: 16px;
        font-weight: bold;
      }
      .info {
        font-size: 12px;
        line-height: 18px;
        margin-top: 3px;
        max-height: 36px;
        overflow: hidden;
      }
    }
    .right {
      width: 40px;
      display: flex;
      align-items: center;
      justify-content: flex-end;
      i {
        font-size: 25px;
      }
    }
  }

  .grid-list {
    height: 55px;
    display: flex;
    .grid-item {
      flex: 1;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      .number {
        font-size: 16px;
        line-height: 26px;
        font-weight: bold;
      }
      .name {
        font-size: 12px;
        color: rgba(255, 255, 255, 0.8);
      }
    }
  }
}
.created-view {
  margin-top: 15px;
  .btn-view {
    display: flex;
    .btn {
      flex: 1;
      color: @blue;
      width: 150px;
      height: 40px;
      border-radius: 20px;
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: 16px;
      background: white;
    }
  }
}
.cell-list {
  .cell {
    margin: 15px;
    padding: 10px;
    border-radius: 10px;
    box-shadow: 1px 1px 6px rgba(0, 0, 0, 0.1);
    display: flex;
    align-items: center;
    color: #666;
    .left {
      width: 28px;
      height: 28px;
      display: flex;
      align-items: center;
      justify-content: center;
      margin-right: 8px;
      i {
        font-size: 22px;
        color: @blue;
      }
    }
    .body {
      flex: 1;
      font-size: 16px;
    }
    .right {
      width: 28px;
      height: 28px;
      display: flex;
      align-items: center;
      justify-content: center;
      i {
        font-size: 22px;
        color: #999;
      }
    }
  }
}
</style>

