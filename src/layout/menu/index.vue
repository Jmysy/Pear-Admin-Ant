<template>
  <div id="menu">
    <a-menu
      v-model:openKeys="openKey"
      v-model:selectedKeys="selectKey"
      :mode="menuModel"
      :theme="theme"
      @openChange="openChange"
    >
      <sub-menu
        v-for="(route, index) in routes" :key="index"
        :item="route"
        :base-path="route.path"
      />
    </a-menu>
  </div>
</template>
<script>
  import { computed, getCurrentInstance , ref} from "vue";
  import { useStore } from "vuex";
  import SubMenu from './SubMenu.vue'

  export default {
    components: {
      SubMenu,
    },
    setup() {
      const { getters, commit } = useStore();

      const { ctx } = getCurrentInstance()
      const routes = computed(()=> ctx.$root.$router.options.routes);

      // 响 应 式 菜 单 选 中
      const selectKey = ref(getters.selectKey);
      const openKey = ref(getters.openKey);
      const menuModel = ref(getters.menuModel);
      const theme = computed(()=> getters.theme);

      // 菜单打开响应时间
      const openChange = function(openKeys){
        commit("layout/updateOpenKey", openKeys)
      }

      return {
        routes,
        selectKey,
        openKey,
        menuModel,
        theme,
        openChange,
      };
    },
  };
</script>
<style>
.ant-menu-sub.ant-menu-inline > .ant-menu-item,
.ant-menu-sub.ant-menu-inline > .ant-menu-submenu > .ant-menu-submenu-title {
  height: 48px;
  line-height: 48px;
}
.ant-menu-vertical > .ant-menu-item,
.ant-menu-vertical-left > .ant-menu-item,
.ant-menu-vertical-right > .ant-menu-item,
.ant-menu-inline > .ant-menu-item,
.ant-menu-vertical > .ant-menu-submenu > .ant-menu-submenu-title,
.ant-menu-vertical-left > .ant-menu-submenu > .ant-menu-submenu-title,
.ant-menu-vertical-right > .ant-menu-submenu > .ant-menu-submenu-title,
.ant-menu-inline > .ant-menu-submenu > .ant-menu-submenu-title {
  height: 48px !important;
  line-height: 48px !important;
}
.ant-layout-sider {
  background: #191a23 !important;
}
.ant-menu-dark,
.ant-menu-dark {
  background: #191a23 !important;
}
.ant-menu-dark .ant-menu-inline.ant-menu-sub {
  background-color: #101117 !important;
}
.ant-menu-vertical .ant-menu-item,
.ant-menu-vertical-left .ant-menu-item,
.ant-menu-vertical-right .ant-menu-item,
.ant-menu-inline .ant-menu-item,
.ant-menu-vertical .ant-menu-submenu-title,
.ant-menu-vertical-left .ant-menu-submenu-title,
.ant-menu-vertical-right .ant-menu-submenu-title,
.ant-menu-inline .ant-menu-submenu-title {
  margin-top: 4px !important;
  margin-bottom: 4px !important;
}
</style>