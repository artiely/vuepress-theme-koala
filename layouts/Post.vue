<template>
  <div style="height:100%">
    <a-layout style="min-height:100%">
      <a-layout-sider
        breakpoint="lg"
        collapsedWidth="0"
        @collapse="onCollapse"
        v-if="sidebarItems.length"
      >
        <transition name="slide-fade">
          <Sidebar
            v-if="!collapse"
            :items="sidebarItems"
            @toggle-sidebar="toggleSidebar"
          >
            <slot
              name="sidebar-top"
              slot="top"
            />
            <slot
              name="sidebar-bottom"
              slot="bottom"
            />
          </Sidebar>
        </transition>

      </a-layout-sider>
      <!-- <a-layout> -->
      <a-layout-content class="my-content">
        <a-layout-header>
          <Navbar
            v-if="shouldShowNavbar"
            @toggle-sidebar="toggleSidebar"
          />
        </a-layout-header>
        <div
          class="theme-container"
          :class="pageClasses"
          @touchstart="onTouchStart"
          @touchend="onTouchEnd"
        >
          <Page :sidebar-items="sidebarItems">
            <slot
              name="page-top"
              slot="top"
            />
            <slot
              name="page-bottom"
              slot="bottom"
            />
          </Page>
        </div>
        <div style="max-width:720px;margin:0 auto">
          <ClientOnly>
            <Vssue />
          </ClientOnly>
        </div>

      </a-layout-content>
      <!-- <a-layout-footer>Footer</a-layout-footer> -->
      <!-- </a-layout> -->
    </a-layout>
  </div>
</template>

<script>
import Page from '@theme/components/Page.vue'
import Sidebar from '@theme/components/Sidebar.vue'
import { resolveSidebarItems } from '../util'
import Navbar from '@theme/components/Navbar.vue'
export default {
  components: {
    Sidebar, Page, Navbar
  },
  data () {
    return {
      isSidebarOpen: false,
      collapse: false
    }
  },
  computed: {
    sidebarItems () {
      return resolveSidebarItems(
        this.$page,
        this.$page.regularPath,
        this.$site,
        this.$localePath
      )
    },
    shouldShowNavbar () {
      const { themeConfig } = this.$site
      const { frontmatter } = this.$page
      if (
        frontmatter.navbar === false
        || themeConfig.navbar === false) {
        return false
      }
      return (
        this.$title
        || themeConfig.logo
        || themeConfig.repo
        || themeConfig.nav
        || this.$themeLocaleConfig.nav
      )
    },
    pageClasses () {
      const userPageClass = this.$page.frontmatter.pageClass
      return [
        {
          'no-navbar': !this.shouldShowNavbar,
          'sidebar-open': this.isSidebarOpen,
          'no-sidebar': !this.shouldShowSidebar
        },
        userPageClass
      ]
    }
  },
  methods: {
    onCollapse (b) {
      this.collapse = b
    },
    toggleSidebar (to) {
      this.isSidebarOpen = typeof to === 'boolean' ? to : !this.isSidebarOpen
    },
    // side swipe
    onTouchStart (e) {
      this.touchStart = {
        x: e.changedTouches[0].clientX,
        y: e.changedTouches[0].clientY
      }
    },

    onTouchEnd (e) {
      const dx = e.changedTouches[0].clientX - this.touchStart.x
      const dy = e.changedTouches[0].clientY - this.touchStart.y
      if (Math.abs(dx) > Math.abs(dy) && Math.abs(dx) > 40) {
        if (dx > 0 && this.touchStart.x <= 80) {
          this.toggleSidebar(true)
        } else {
          this.toggleSidebar(false)
        }
      }
    }
  },
  created () {
  }
}
</script>

<style>
/* .my-content {
  background: url(data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAQAAAAECAYAAACp8Z5+AAAACXBIWXMAAAsSAAALEgHS3X78AAAAKUlEQVQImWPU2ZIiyYAEWBgYGN4i8YUZ////D+fpbk2VZEJWzsDAwAAABAkGw98D+xYAAAAASUVORK5CYII=);
} */
.ant-layout-sider-zero-width-trigger {
  top: 7.5rem;
}
.ant-layout-sider {
  background-color: rgba(0, 21, 41, 0.8) !important;
  /* background-image: url("../layouts/bg10.png"); */
}
#app,
.main-content {
  height: 100%;
}
.slide-fade-enter-active {
  transition: all 0.18s ease;
}
.slide-fade-enter, .slide-fade-leave-to
/* .slide-fade-leave-active for below version 2.1.8 */ {
  transform: translateX(-200px);
  opacity: 0;
}
</style>
