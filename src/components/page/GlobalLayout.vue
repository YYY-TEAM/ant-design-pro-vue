<template>
  <a-layout class="layout" :class="device">

    <template v-if="layoutMode === 'sidemenu'">
      <a-drawer
        v-if="device === 'mobile'"
        :wrapClassName="'drawer-sider ' + theme"
        placement="left"
        @close="() => this.collapsed = false"
        :closable="false"
        :visible="collapsed"
      >
        <side-menu
          mode="inline"
          :menus="menus"
          @menuSelect="menuSelect"
          :theme="theme"
          :collapsed="false"
          :collapsible="true"></side-menu>
      </a-drawer>

      <side-menu
        v-else
        :menus="menus"
        :theme="theme"
        :mode="menuMode"
        :collapsed="collapsed"
        :collapsible="true"></side-menu>
    </template>

    <a-layout :class="[layoutMode]">
      <!-- layout header -->
      <global-header :mode="layoutMode" :theme="theme" :collapsed="collapsed" :device="device" @toggle="toggle"/>

      <!-- layout content -->
      <a-layout-content :style="{ margin: '24px 24px 0', height: '100%' }">
        <slot></slot>
      </a-layout-content>

      <!-- layout footer -->
      <a-layout-footer style="padding: 0px">
        <global-footer />
      </a-layout-footer>
    </a-layout>

    <setting-drawer></setting-drawer>
  </a-layout>
</template>

<script>
  import SideMenu from '@/components/menu/SideMenu'
  import GlobalHeader from '@/components/page/GlobalHeader'
  import GlobalFooter from '@/components/page/GlobalFooter'
  import SettingDrawer from '@/components/setting/SettingDrawer'
  import { triggerResize } from '@/utils/util'
  import { mapState, mapActions } from 'vuex'
  export default {
    name: "BasicLayout",
    components: {
      SideMenu,
      GlobalHeader,
      GlobalFooter,
      SettingDrawer
    },
    data () {
      return {
        // light, dark
        menuTheme: 'light',
        // inline, horizontal
        menuMode: 'inline',
        collapsed: false,
        menus: []
      }
    },
    computed: {
      ...mapState({
        mainMenu: state => state.permission.addRouters,
        layoutMode: state => state.app.layout,
        sidebarOpened: state => state.app.sidebar.opened,
        theme: state => state.app.theme,
        device: state => state.app.device,
      })
    },
    created() {
      this.menus = this.mainMenu.find((item) => item.path === '/').children
    },
    methods: {
      ...mapActions(['setSidebar']),
      toggle() {
        this.collapsed = !this.collapsed
        triggerResize()
        this.setSidebar(this.collapsed)
      },
      menuSelect() {
        if (this.device !== 'desktop') {
          this.collapsed = false
        }
      }
    },
  }
</script>

<style lang="scss">
  body {
    // 打开滚动条固定显示
    overflow-y: scroll;

    &.colorWeak {
      filter: invert(80%);
    }
  }

  .layout {
    min-height: 100vh;
    overflow-x: hidden;

    &.mobile {

      .ant-layout-content {

        .content {
          margin: 24px 0 0;
        }
      }

      .ant-table-wrapper {
        .ant-table-body {
          overflow-y: auto;
        }
      }
    }

    &.ant-layout-has-sider {
      flex-direction: row;
    }

    .trigger {
      font-size: 20px;
      line-height: 64px;
      padding: 0 24px;
      cursor: pointer;
      transition: color .3s;
      &:hover {
        background: rgba(0, 0, 0, 0.025);
      }
    }

    .header {
      height: 64px;
      padding: 0 12px 0 0;
      background: #fff;
      box-shadow: 0 1px 4px rgba(0, 21, 41, .08);
      position: relative;
    }

    .header, .top-nav-header-index {

      .user-wrapper {
        float: right;
        height: 100%;

        .action {
          cursor: pointer;
          padding: 0 12px;
          display: inline-block;
          transition: all .3s;
          height: 100%;

          &:hover {
            background: rgba(0, 0, 0, 0.025);
          }

          .avatar {
            margin: 20px 8px 20px 0;
            color: #1890ff;
            background: hsla(0, 0%, 100%, .85);
            vertical-align: middle;
          }


          .icon {
            font-size: 16px;
            padding: 4px;
          }
        }
      }

      &.dark {
        .user-wrapper {

          .action {
            color: rgba(255, 255, 255, 0.85);

            &:hover {
              background: rgba(255, 255, 255, 0.16);
            }
          }
        }
      }
    }

    .top-nav-header-index {
      box-shadow: 0 1px 4px rgba(0,21,41,.08);
      position: relative;
      transition: background .3s,width .2s;

      .header-index-wide {
        max-width: 1200px;
        margin: auto;
        padding-left: 0;
        display: flex;
        height: 64px;

        .ant-menu.ant-menu-horizontal {
          border: none;
          height: 64px;
          line-height: 64px;
        }

        .header-index-left {
          flex: 1 1;
          display: flex;

          .logo.top-nav-header {
            width: 165px;
            height: 64px;
            position: relative;
            line-height: 64px;
            transition: all .3s;
            overflow: hidden;

            img {
              display: inline-block;
              vertical-align: middle;
              height: 32px;
            }

            h1 {
              color: #fff;
              display: inline-block;
              vertical-align: top;
              font-size: 16px;
              margin: 0 0 0 12px;
              font-weight: 400;
            }
          }
        }

        .header-index-right {
          float: right;
          height: 64px;
          overflow: hidden;
        }
      }

      &.light {
        background-color: #fff;

        .header-index-wide {
          .header-index-left {
            .logo {
              h1 {
                color: #002140;
              }
            }
          }
        }
      }
    }


    // 内容区
    .layout-content {
      margin: 24px 24px 0px;
      height: 100%;
      height: 64px;
      padding: 0 12px 0 0;
    }

  }

  .topmenu {
    .page-header-index-wide {
      max-width: 1200px;
      margin: 0 auto;
    }
  }

  // drawer-sider 自定义
  .ant-drawer.drawer-sider {
    .sider {
      box-shadow: none;
    }

    &.dark {
      .ant-drawer-content {
        background-color: rgb(0, 21, 41);
      }
    }
    &.light {
      box-shadow: none;
      .ant-drawer-content {
        background-color: #fff;
      }
    }

    .ant-drawer-body {
      padding: 0
    }
  }

  // 菜单样式
  .sider {
    box-shadow: 2px 0 6px rgba(0, 21, 41, .35);
    position: relative;
    z-index: 10;

    .logo {
      height: 64px;
      position: relative;
      line-height: 64px;
      padding-left: 24px;
      -webkit-transition: all .3s;
      transition: all .3s;
      background: #002140;
      overflow: hidden;

      img, h1 {
        display: inline-block;
        vertical-align: middle;
      }

      img {
        height: 32px;
      }

      h1 {
        color: #fff;
        font-size: 20px;
        margin: 0 0 0 12px;
        font-family: "Chinese Quote", -apple-system, BlinkMacSystemFont, "Segoe UI", "PingFang SC", "Hiragino Sans GB", "Microsoft YaHei", "Helvetica Neue", Helvetica, Arial, sans-serif, "Apple Color Emoji", "Segoe UI Emoji", "Segoe UI Symbol";
        font-weight: 600;
      }
    }

    &.light {
      background-color: #fff;
      box-shadow: 2px 0px 8px 0px rgba(29, 35, 41, 0.05);

      .logo {
        background: #fff;
        box-shadow: 1px 1px 0px 0px #e8e8e8;

        h1 {
          color: unset;
        }
      }

      .ant-menu-light {
        border-right-color: transparent;
      }
    }

  }

  // 外置的样式控制
  .user-dropdown-menu-wrapper.ant-dropdown-menu {
    padding: 4px 0;

    .ant-dropdown-menu-item {
      width: 160px;
    }

    .ant-dropdown-menu-item > .anticon:first-child,
    .ant-dropdown-menu-item > a > .anticon:first-child,
    .ant-dropdown-menu-submenu-title > .anticon:first-child
    .ant-dropdown-menu-submenu-title > a > .anticon:first-child {
      min-width: 12px;
      margin-right: 8px;
    }

  }

  // 数据列表 样式
  .table-alert {
    margin-bottom: 16px;
  }

  .table-page-search-wrapper {

    .ant-form-inline {

      .ant-form-item {
        display: flex;
        margin-bottom: 24px;
        margin-right: 0;

        .ant-form-item-control-wrapper {
          flex: 1 1;
          display: inline-block;
          vertical-align: middle;
        }

        >.ant-form-item-label {
          line-height: 32px;
          padding-right: 8px;
          width: auto;
        }
        .ant-form-item-control {
          height: 32px;
          line-height: 32px;
        }
      }
    }

    .table-page-search-submitButtons {
      display: block;
      margin-bottom: 24px;
      white-space: nowrap;
    }

  }

  .content {

    .table-operator {
      margin-bottom: 18px;

      button {
        margin-right: 8px;
      }
    }
  }
</style>