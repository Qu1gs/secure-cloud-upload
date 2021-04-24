<template>
<v-container fill-height justify-center>
    <v-row class="justify-center">
        <LoginCard/>
    </v-row>
</v-container>
</template>
<script>
import LoginCard from '../components/LoginCard.vue'
export default {
  name: 'Login',
  components: {
      LoginCard
  },
  data () {
      return {

      }
  }
}
</script>