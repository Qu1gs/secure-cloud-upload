<template>
<v-container justify-center fluid fill-height>
    <Appbar/>
    <v-row class="justify-center">
        <RegisterCard/>
    </v-row>
</v-container>
</template>
<script>
import RegisterCard from '../components/RegisterCard.vue'
import Appbar from '../components/Appbar.vue'
export default {
  name: 'Login',
  components: {
      Appbar,
      RegisterCard
  },
  data () {
      return {
      }
  }
}
</script>