<template>
  <Menu mode="horizontal" class="menu-top">
    <!-- 收缩侧边栏按钮 -->
    <Button type="text" class="button-icon" @click="changeSideBar">
      <Icon type="md-menu" size="25" />
    </Button>
    <!-- 账户信息 -->
    <div class="account fr">
      <span>Hi，欢迎您，</span>
      <Submenu name="1" style="z-index:10">
        <template slot="title">
          <img class="account-face" :src="user.face" @error="notFoundPic" />
          <strong>{{ user.nickName }}</strong>
        </template>
        <Menu-item name="personalProfile" class="dropdown-item">
          <router-link :to="{ name: 'Profile'}">个人资料</router-link>
        </Menu-item>
        <Menu-item name="changePassword" class="dropdown-item">
          <router-link :to="{ name: 'ChangePwd'}">修改密码</router-link>
        </Menu-item>
        <Menu-item name="logOut" class="dropdown-item">
          <a @click="logOut">退出登录</a>
        </Menu-item>
      </Submenu>
      <!-- 退出全屏 -->
      <Button v-if="setFull" type="text" class="button-icon" @click="fullExit">
        <Icon type="md-contract" size="25" />
      </Button>
      <!-- 全屏 -->
      <Button v-else type="text" class="button-icon" @click="fullScreen">
        <Icon type="md-expand" size="25" />
      </Button>
    </div>
  </Menu>
</template>

<script>
import Common from "common/common.js";
import { SetDefaultPic } from "utils";
import { mapGetters } from "vuex";

export default {
  computed: {
    ...mapGetters(["sidebarSpan", "user"])
  },
  data() {
    return {
      // 是否设置全屏
      setFull: false
    };
  },
  methods: {
    // 改变侧边栏
    changeSideBar() {
      if (this.sidebarSpan === 3) this.$store.commit("SET_SIDEBAR_SPAN", 1);
      else this.$store.commit("SET_SIDEBAR_SPAN", 3);
    },
    // 全屏显示
    fullScreen() {
      this.setFull = true;
      let e = document.documentElement;

      if (e.requestFullscreen) e.requestFullscreen();
      else if (e.mozRequestFullScreen) e.mozRequestFullScreen();
      else if (e.webkitRequestFullscreen) e.webkitRequestFullscreen();
      else if (e.msRequestFullscreen) e.msRequestFullscreen();
    },
    // 退出全屏
    fullExit() {
      this.setFull = false;
      if (document.exitFullscreen) document.exitFullscreen();
      else if (document.mozCancelFullScreen) document.mozCancelFullScreen();
      else if (document.webkitExitFullscreen) document.webkitExitFullscreen();
    },
    // 登出
    logOut() {
      this.$store
        .dispatch("LogOut")
        .then(res => {
          if (res) {
            this.$Message.success("已退出!");
            this.$router.push({ name: "Login" });
          }
        })
        .catch(err => console.log(err));
    },
    // 无法显示图片
    notFoundPic: event => {
      SetDefaultPic(event, 1);
    }
  }
};
</script>

<style lang="less">
.menu-top {
  background: #fff;
}
.account {
  span,
  strong {
    float: left;
    margin-right: 10px;
  }

  .ivu-select-dropdown {
    .ivu-menu-item.dropdown-item {
      padding: 0;
    }
    a {
      padding: 10px 26px 12px 20px;
      display: block;
      color: #495060;
    }
    .ivu-menu-item-selected:hover,
    .ivu-menu-item-active:hover {
      a {
        color: #495060;
      }
    }
  }
}
.account-face {
  float: left;
  margin-top: 5px;
  margin-right: 15px;
  width: 40px;
  height: 40px;
  border: 1px solid #eee;
  border-radius: 20px;
  -webkit-border-radius: 20px;
  -o-border-radius: 20px;
}

.button-icon {
  &:focus {
    box-shadow: none;
  }
}
</style>
