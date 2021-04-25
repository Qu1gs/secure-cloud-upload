<template>
    <v-app-bar
      color="deep-purple"
      dark
      absolute
    >
      <v-toolbar-title>Title</v-toolbar-title>
      <v-spacer/>
      <div v-if="user">{{ user.email}}</div>
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
    }
}
</script>
