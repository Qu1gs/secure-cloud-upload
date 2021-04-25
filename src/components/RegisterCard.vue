<template>
    <v-card color="FFFFFF0" elevation="24" outlined width="400" height="300" tile >
          <v-card color="deep-orange" style="margin-bottom: 10px" tile width="400" dark>
           <v-card-title class="justify-center">Register</v-card-title>
        </v-card>
                 <v-row class="justify-center">
                     <v-col
                        cols="12"
                        sm="9">
                     <v-text-field
                        v-model="email"
                        :rules="[rules.required, rules.min]"
                        label="Email"
                        hint="At least 8 characters"
                        counter
                        @click:append="show1 = !show1"
                    ></v-text-field>
                    </v-col>
                 </v-row>
                 <v-row class="justify-center">
                     <v-col
                        cols="12"
                        sm="9">
                     <v-text-field
                        v-model="password"
                        :append-icon="show1 ? 'mdi-eye' : 'mdi-eye-off'"
                        :rules="[rules.required, rules.min]"
                        :type="show1 ? 'text' : 'password'"
                        label="Password"
                        hint="At least 8 characters"
                        counter
                        @click:append="show1 = !show1"
                    ></v-text-field>
                    </v-col>
                 </v-row>
                 <v-row class="justify-center">
                 <v-btn color="primary" @click.native="register()">Register</v-btn>
                 </v-row><v-row class="justify-center">
                 </v-row>
        </v-card>
</template>

<script>
import firebase from 'firebase'
export default {
  name: 'LoginCard',
  data () {
    return {
      show1: false,
      password: "",
      email: "",
        rules: {
          required: value => !!value || 'Required.',
          min: v => v.length >= 8 || 'Min 8 characters'
        },
    }
  },
  methods: {
    register() {
    firebase
      .auth()
      .createUserWithEmailAndPassword(this.email, this.password)
      .then(() => {
       var ref = firebase.firestore().collection("users").doc(this.email);
                ref.set({
                    email: this.email,
                    groups: []
                })
                .catch((error) => {
                    console.error("Error writing document: ", error);
                });
        this.$router.push('/');
      })
      .catch(error => {
        console.log(error.message);
      });
  },
}
}
</script>
