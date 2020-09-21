<template>
  <v-main class="grey lighten-3">
    <nuxt />

    <core-footer />
  </v-main>
</template>

<script>
import Footer from '@/components/core/Footer'

export default {
  // name: 'DashboardCoreView',

  components: {
    CoreFooter: Footer
  }
}
</script>
