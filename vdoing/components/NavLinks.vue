<template>
  <nav class="nav-links" v-if="allLinks.length">
    <!-- user links and English Website link -->
    <div
        class="nav-item"
        v-for="item in allLinks"
        :key="item.link"
    >
      <a
          v-if="item.isEnglishSite"
          :href="item.link"
          class="repo-link"
          target="_blank"
          rel="noopener noreferrer"
      >
        {{ item.label }}
        <OutboundLink />
      </a>
      <DropdownLink
          v-else-if="item.type === 'links'"
          :item="item"
      />
      <NavLink
          v-else
          :item="item"
      />
    </div>
  </nav>
</template>

<script>
import DropdownLink from '@theme/components/DropdownLink.vue'
import NavLink from '@theme/components/NavLink.vue'
import { resolveNavLinkItem } from '../util'

export default {
  components: { NavLink, DropdownLink },

  computed: {
    userNav() {
      return this.$themeLocaleConfig.nav || this.$site.themeConfig.nav || []
    },

    allLinks() {
      const userLinks = (this.userNav || []).map(link => {
        return Object.assign(resolveNavLinkItem(link), {
          items: (link.items || []).map(resolveNavLinkItem)
        })
      })

      // 添加 English Website 链接
      const englishWebsiteLink = {
        link: 'https://en.orionai.top/',
        label: 'English Website',
        isEnglishSite: true // 用于模板中的条件渲染
      }

      return [...userLinks, englishWebsiteLink]
    },
  }
}
</script>

<style lang="stylus">
.nav-links
  display inline-block
  a
    line-height 1.4rem
    color inherit
    &:hover, &.router-link-active
      color $accentColor
  .nav-item
    position relative
    display inline-block
    margin-left 1.5rem
    line-height 2rem
    &:first-child
      margin-left 0
  .repo-link
    margin-left 1.5rem
// 959
@media (max-width $MQNarrow)
  .nav-links
    .nav-item
      margin-left 1.2rem
@media (max-width $MQMobile)
  .nav-links
    .nav-item, .repo-link
      margin-left 0
@media (min-width $MQMobile)
  .nav-links a
    &:hover, &.router-link-active
      color var(--textColor)
  .nav-item > a:not(.external)
    &:hover, &.router-link-active
      margin-bottom -2px
      border-bottom 2px solid lighten($accentColor, 8%)
</style>
