<template>
  <!-- 框架顶部菜单区域 -->
  <div id="header">
    <template v-if="layout !== 'layout-head'">
      <!-- 左侧菜单功能项 -->
      <div class="prev-menu">
        <!-- 左侧缩进功能键 -->
        <div class="menu-item" @click="trigger()">
          <AlignLeftOutlined v-if="collapsed" />
          <!-- 左侧缩进功能键盘 -->
          <AlignRightOutlined v-else />
        </div>
        <div class="menu-item" @click="refresh">
          <!-- 刷新当前页面路由 -->
          <ReloadOutlined />
        </div>
      </div>
    </template>
    <template v-else>
      <div class="head-logo">
        <Logo></Logo>
      </div>
      <div class="head-menu">
        <Menu></Menu>
      </div>
    </template>

    <!-- 实现综合布局方式 -->
    <div v-if="layout == 'layout-comp'" class="comp-menu">
      <template :key="index" v-for="(route, index) in routes">
        <router-link
          :to="toPath(route)"
          class="menu-item"
          :class="[active === route.path ? 'is-active' : '']"
        >
          <span>{{ route.meta.title }}</span>
        </router-link>
      </template>
    </div>

    <!-- 右侧菜单功能项 -->
    <div class="next-menu">
      <div class="menu-item" v-if="!fullscreen" @click="full(1)">
        <!-- 当前页面最大化 -->
        <ExpandOutlined />
      </div>
      <div class="menu-item" v-else @click="full(2)">
        <!-- 当前页面最大化 -->
        <CompressOutlined />
      </div>
      <div class="menu-item">
        <a-dropdown v-model:visible="visible">
          <BellOutlined />
          <template #overlay>
            <a-menu>
              <a-menu-item key="0">
                <a
                  target="_blank"
                  rel="noopener noreferrer"
                  href="http://www.alipay.com/"
                  >1st menu item</a
                >
              </a-menu-item>
              <a-menu-item key="1">
                <a
                  target="_blank"
                  rel="noopener noreferrer"
                  href="http://www.taobao.com/"
                  >2nd menu item</a
                >
              </a-menu-item>
              <a-menu-divider />
              <a-menu-item key="3" disabled>
                3rd menu item（disabled）
              </a-menu-item>
            </a-menu>
          </template>
        </a-dropdown>
      </div>

      <div class="menu-item">
        <!-- 国际化切换 -->
        <GlobalOutlined />
      </div>
      <a-dropdown  class="avatar-item">
        <a-avatar
          src="https://portrait.gitee.com/uploads/avatars/user/1611/4835367_Jmysy_1578975358.png"
        ></a-avatar>
        <template #overlay>
          <a-menu>
            <a-menu-item key="0">
              <a
                target="_blank"
                rel="noopener noreferrer"
                href="http://www.alipay.com/"
                >1st menu item</a
              >
            </a-menu-item>
            <a-menu-item key="1">
              <a
                target="_blank"
                rel="noopener noreferrer"
                href="http://www.taobao.com/"
                >2nd menu item</a
              >
            </a-menu-item>
            <a-menu-divider />
            <a-menu-item key="3" disabled>
              3rd menu item（disabled）
            </a-menu-item>
          </a-menu>
        </template>
      </a-dropdown>
      <div class="menu-item" @click="setting()">
        <!-- 主题设置隐显键 -->
        <MoreOutlined />
      </div>
    </div>
  </div>
</template>
<script>
import { computed, watch, getCurrentInstance, ref } from "vue";
import { useStore } from "vuex";
import Menu from "../menu/index.vue";
import Logo from "../logo/index.vue";
import _path from "path";
import {
  AlignLeftOutlined,
  AlignRightOutlined,
  MoreOutlined,
  ExpandOutlined,
  CompressOutlined,
  ReloadOutlined,
  GlobalOutlined,
  BellOutlined,
} from "@ant-design/icons-vue";
export default {
  components: {
    AlignLeftOutlined,
    AlignRightOutlined,
    MoreOutlined,
    ExpandOutlined,
    CompressOutlined,
    ReloadOutlined,
    GlobalOutlined,
    Menu,
    Logo,
    BellOutlined,
  },
  methods: {
    full: function (num) {
      num = num || 1;
      num = num * 1;
      var docElm = document.documentElement;
      switch (num) {
        case 1:
          if (docElm.requestFullscreen) {
            docElm.requestFullscreen();
          } else if (docElm.mozRequestFullScreen) {
            docElm.mozRequestFullScreen();
          } else if (docElm.webkitRequestFullScreen) {
            docElm.webkitRequestFullScreen();
          } else if (docElm.msRequestFullscreen) {
            docElm.msRequestFullscreen();
          }
          break;
        case 2:
          if (document.exitFullscreen) {
            document.exitFullscreen();
          } else if (document.mozCancelFullScreen) {
            document.mozCancelFullScreen();
          } else if (document.webkitCancelFullScreen) {
            document.webkitCancelFullScreen();
          } else if (document.msExitFullscreen) {
            document.msExitFullscreen();
          }
          break;
      }
      this.updateFullscreen();
    },
  },
  setup() {
    const { getters, commit } = useStore();
    const layout = computed(() => getters.layout);
    const collapsed = computed(() => getters.collapsed);
    const fullscreen = computed(() => getters.fullscreen);
    const menuModel = computed(() => getters.menuModel);
    const theme = computed(() => getters.theme);
    const { ctx } = getCurrentInstance();

    const $route = computed(() => ctx.$root.$route);
    const active = ref($route.value.matched[0].path);
    watch($route, (to) => (active.value = to.matched[0].path));

    //计算点击跳转的最终路由
    const toPath = (route) => {
      let { redirect, children, path } = route;
      if (redirect) {
        return redirect;
      }
      while (children && children[0]) {
        path = _path.resolve(path, children[0].path);
        children = children[0].children;
      }
      return path;
    };

    const { $router } = ctx.$root;
    const routes = ref($router.options.routes.filter((r) => !r.hidden));
    //实现当前路由刷新
    const refresh = () => {
      let _route = $route.value;
      $router.replace({
        path: _route.path,
        params: _route.params,
        query: {
          ..._route.query,
          _: Date.now(),
        },
      });
    };

    return {
      layout,
      collapsed,
      fullscreen,
      trigger: () => commit("layout/TOGGLE_SIDEBAR"),
      setting: () => commit("layout/TOGGLE_SETTING"),
      updateFullscreen: () => commit("layout/updateFullscreen"),
      menuModel,
      theme,
      refresh,
      routes,
      active,
      toPath,
    };
  },
};
</script>
