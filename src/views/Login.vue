<template>
<v-container justify-center fluid fill-height>
    <Appbar/>
    <v-row class="justify-center">
        <LoginCard/>
    </v-row>
</v-container>
</template>
<script>
import LoginCard from '../components/LoginCard.vue'
import Appbar from '../components/Appbar.vue'
export default {
  name: 'Login',
  components: {
      LoginCard,
      Appbar,
  },
  data () {
      return {
      }
  }
}
</script>