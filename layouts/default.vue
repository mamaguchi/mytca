<template>
  <v-app>
    <core-app-bar />
    <core-drawer />
    <core-view />
    <core-settings />
  </v-app>
</template>

<script>
import AppBar from '@/components/core/AppBar'
import Drawer from '@/components/core/Drawer'
import View from '@/components/core/View'
import Settings from '@/components/core/Settings'

export default {
  components: {
    CoreAppBar: AppBar,
    CoreDrawer: Drawer,
    CoreView: View,
    CoreSettings: Settings
  },
  // props: {
  //   source: String
  // },
  data: () => ({
    // drawer: null,
    expandOnHover: false
  })
}
</script>
