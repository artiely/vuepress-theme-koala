<template>
  <!-- class="theme-container" -->
  <div style="height:100%">
    <header>
      <!-- <Menu></Menu> -->
      <!-- <Navbar v-if="shouldShowNavbar" /> -->
    </header>
    <componen
      class="main-content"
      :is="layout"
    />
  </div>
</template>

<script>
import home from './Home'
import post from './Post'

export default {

  computed: {
    layout () {
      const { path } = this.$page
      if (path === '/' || path === '/zh/') {
        return home
      } else {
        return post
      }
    },
    // shouldShowNavbar () {
    //   const { themeConfig } = this.$site
    //   const { frontmatter } = this.$page
    //   if (
    //     frontmatter.navbar === false
    //     || themeConfig.navbar === false) {
    //     return false
    //   }
    //   return (
    //     this.$title
    //     || themeConfig.logo
    //     || themeConfig.repo
    //     || themeConfig.nav
    //     || this.$themeLocaleConfig.nav
    //   )
    // },
  }
}
</script>

