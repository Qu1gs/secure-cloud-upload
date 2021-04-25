<template>
<v-container justify-center fluid fill-height>
    <Appbar/>
    <v-row class="justify-center">
        <RegisterCard v-if="this.switch === 1"/>
        <LoginCard v-if="this.switch === 0"/>
    </v-row>
</v-container>
</template>
<script>
import LoginCard from '../components/LoginCard.vue'
import RegisterCard from '../components/RegisterCard.vue'
import Appbar from '../components/Appbar.vue'
export default {
  name: 'Login',
  components: {
      LoginCard,
      Appbar,
      RegisterCard
  },
  data () {
      return {
          switch: 0
      }
  }
}
</script>