<template>
    <v-app-bar
      color="deep-purple"
      dark
      absolute
    >
      <v-toolbar-title>Secure Upload</v-toolbar-title>
      <v-spacer/>
      <div v-if="!user"><v-btn text color="red" @click="register()">Register</v-btn></div>
      <div v-if="user">{{ user.email}}</div>
      <v-btn v-if="user" icon @click="logout()"><v-icon color="red">mdi-logout</v-icon></v-btn>
    </v-app-bar>
</template>

<script>
import firebase from 'firebase'
  export default {
    name: 'Appbar',
    data () {
        return {
            group: null,
            user: firebase.auth().currentUser
        }
    },
    methods: {
      logout(){
        firebase.auth().signOut()
        this.$router.push('/');

      },
      register(){
        this.$router.push('/register')
      }
    }
}
</script>
