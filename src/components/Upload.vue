<template>
<v-row class="justify-center">
        <v-card tile width="400" height="400" elevation="24">
          <v-card width="400" class="text-center text-h5" color="deep-purple" height="45" dark tile> Upload Files</v-card>
          <div style="padding:20px">
          <div class="text-h6 text-center" style="margin-bottom: 50px; margin-top:20px" >You have no files shared or uploaded!</div>
          <div class="subtitle-2 text-center" >Upload files below</div>
          <v-file-input
            v-model="files"
            color="deep-purple accent-4"
            counter
            label="File input"
            multiple
            placeholder="Select your files"
            prepend-icon="mdi-paperclip"
            outlined
            :show-size="1000"
          >
            <template v-slot:selection="{ index, text }">
              <v-chip
                v-if="index < 2"
                color="deep-purple accent-4"
                dark
                label
                small
              >
                {{ text }}
              </v-chip>

              <span
                v-else-if="index === 2"
                class="overline grey--text text--darken-3 mx-2"
              >
                +{{ files.length - 2 }} File(s)
              </span>
            </template>
         </v-file-input>
         </div>
         <v-row class="justify-center">
         <v-btn color="primary" @click.native="upload()">Upload</v-btn>
         </v-row>
        </v-card>
    </v-row>
</template>
<script>
import firebase from 'firebase'
export default {
  name: 'Upload',
  data () {
      return {
        files: [],
        user: firebase.auth().currentUser
      }
  },
  methods: {
    uploader (datum){
      console.log(datum)
    },
    upload () {
      for(var i = 0; i < this.files.length; i++){
      const file = this.files[i];
      const reader = new FileReader();
      var output = ""
      reader.onload = e => output += (e.target.result);
            reader.readAsText(file);
            reader.onloadend = () => this.uploader(output)
      }
      this.files = []
    }
  }
}
</script>