<template>
  <v-app>
    <v-main>
      <Appbar/>
      <router-view/>
    </v-main>
  </v-app>
</template>

<script>
import Appbar from '../src/components/Appbar.vue'
export default {
  name: 'App',
  components: {
    Appbar
  },
  data: () => ({
    //
  }),
};
</script>
