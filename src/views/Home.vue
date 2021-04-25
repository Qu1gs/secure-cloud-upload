<template>
  <v-container justify-center fluid fill-height>
    <Appbar/>
    <v-row class="justify-center">
    <v-card width="1400px" height="700" color="white" tile>
      <v-card color="deep-purple" width="1400px" tile dark>
      <v-card-title>Groups</v-card-title>
    </v-card>
      <v-dialog
                v-model="dialogCreateGroup"
                max-width="1000"
                transition="dialog-bottom-transition"
              >
            <template v-slot:activator="{ on, attrs }">
            <v-row>
            <v-btn
              style="margin:40px"
              color="primary"
              v-bind="attrs"
              v-on="on"
            >
              Create Group 
            </v-btn>
          <v-spacer/>
          <v-spacer/>
          </v-row>
          </template>
          <v-card color="deep-purple" tile class="white--text">
            <v-card-title>
              <v-spacer/>
              <span>Create New Group</span>
              <v-spacer/>
            </v-card-title>
          </v-card>
            <v-card>
                <v-form v-model="groupValid">
                  <v-text-field
                        filled
                        label="Group Name"
                        placeholder="Group Name"
                        v-model="groupName"
                        :rules="[v => !!v || 'Group Name is required']"
                        required
                        dense
                      />
                      <v-autocomplete
                        v-model="members"
                        filled
                        chips
                        :items="listOfUsers"
                        label="Members"
                        item-text="name"
                        item-value="name"
                        multiple
                        dense
                      />
                </v-form>
                <v-card-actions>
                  <v-spacer></v-spacer>
                  <v-btn
                    color="red"
                    text
                    @click="dialogCreateGroup = false"
                  >
                    Cancel
                  </v-btn>
                  <v-btn
                    color="green darken-2"
                    text
                    :disabled="!(groupValid)"
                    @click="createKeyPairings()"
                  >
                  Save
                  <v-icon
                    right
                    dark
                  >
                  mdi-cloud-upload
                </v-icon>
                  </v-btn>
                </v-card-actions>
            </v-card>
            </v-dialog>
                    <v-dialog
                      v-model="viewDialog"
                      max-width="1400px"
                      min-height="600px"
                    >
                      <v-card min-height="800px">
                        <v-card-title>
                          <div class="headline text-center">{{ this.selectedItem.name }}</div><v-file-input
                            v-model="files"
                            color="deep-purple accent-4"
                            counter
                            label="File input"
                            multiple
                            placeholder="Select your files"
                            prepend-icon="mdi-paperclip"
                            style="margin-left: 10px; margin-right: 10px"
                            :show-size="1000"
                          />
                          <v-btn @click="upload(selectedItem)">Upload</v-btn>
                        </v-card-title>
                        <v-data-table
                        height="600px"
                        style="padding: 20px"
                        :items-per-page="5"
                        :headers="itemHeaders"
                        :items="this.uploadedFiles"
                        >
                        <template v-slot:[`item.actions`]="{ item }">
                          <v-icon small class="mr-2" @click="download(item)">
                            mdi-download
                          </v-icon>
                          <v-icon
                            small
                            @click="deleteFile(item)"
                            >
                            mdi-delete
                          </v-icon>
                        </template>
                      </v-data-table>
                        <v-card-actions>
                        </v-card-actions>
                      </v-card>
                    </v-dialog>
            <v-data-table
              height="450px"
              style="padding: 20px"
              :items-per-page="5"
              :headers="headers"
              :items="this.groups"
            >
              <template v-slot:[`item.actions`]="{ item }">
                <v-icon small class="mr-2" @click="viewGroup(item); getDownload(item)">
                  mdi-eye
                </v-icon>
                <v-icon
                  small
                  @click="deleteGroup(item)"
                  >
                  mdi-delete
                </v-icon>
              </template>
            </v-data-table>
    </v-card>
    </v-row>
</v-container>
</template>
<script>
import Appbar from '../components/Appbar.vue'
import firebase from 'firebase'
var fileDownload = require('js-file-download');
import { Crypt, RSA } from 'hybrid-crypto-js';
export default {
  name: 'Home',
  components: {
    Appbar
  },
  data () {
      return {
        dialogCreateGroup: false,
        deleteGroupDialog: false,
        groups: [],
        groupValid: false,
        groupName: null,
        members: [],
        listOfUsers: [],
        selectedItem: [],
        uploadedFiles: [],
        viewDialog: null,
        priv: null,
        files: [],
        headers: [
        {
          text: 'Group Name',
          sortable: true,
          value: 'name',
          width: '150',
          groupable: false
        },
        {
          text: 'Owner',
          sortable: true,
          value: 'author',
          width: '150',
          groupable: false
        },
        {
          text: 'View / Delete',
          value: 'actions',
          sortable: false,
          align: 'right'
        }
      ],
      itemHeaders: [
        {
          text: 'Name',
          sortable: true,
          value: 'path_',
          width: '400',
          groupable: false
        },
        {
          text: 'Download / Delete',
          value: 'actions',
          sortable: false,
          align: 'right'
        }
      ]
      }
  },
  created () {
    this.getUsers()
    this.getGroups()
  },
  methods: {
    deleteFile(item){
      firebase.storage().ref(item.path_).delete()
      this.getDownload()
    },
    download(item) {
      firebase.storage().ref(item.path_).getDownloadURL()
      .then((url) => {
        var xhr = new XMLHttpRequest();
        xhr.responseType = 'text';
        xhr.onload = () => {
          var blob = xhr.response;
          firebase.firestore().collection("users").doc(firebase.auth().currentUser.email).get().then((doc) => {
            for(var i = 0; i < doc.data().groups.length; i++){
              if(doc.data().groups[i] === this.selectedItem.name){
                this.$data.priv = doc.data().key[i]
              }
            }
            console.log(blob)
            var crypt = new Crypt();
            var decrypted =  crypt.decrypt(this.$data.priv, blob)
            var buf = new ArrayBuffer(decrypted.message.length*2);
            var bufView = new Uint8Array(buf)
            var strLen = decrypted.message.length
            for(var j =0; j<strLen; j++){
                bufView[j] = decrypted.message.charCodeAt(j)
            }
            fileDownload((buf), item.path_)
            this.$data.priv = null
          })
        };
        xhr.open('GET', url);
        xhr.send();
      })
      .catch((error) => {
        console.log(error)
      });
    },
    async upload(item) {
      var files = this.files
      for(var i = 0; i < files.length; i++){
      const file = files[i]
      var arrBuff = await file.arrayBuffer()
      var text = String.fromCharCode.apply(null, new Uint8Array(arrBuff));
      var crypt = new Crypt();
      var encrypted = await crypt.encrypt(this.selectedItem.publicKey, text)
      firebase.storage().ref(item.name+"/"+file.name).putString(encrypted).then(() => {
        this.getDownload(item)
      });
      }
      this.files = []
    },
    getDownload(item) {
      this.$data.uploadedFiles = []
      console.log(this.$data.groups)
      firebase.storage().ref(item.name+"/").listAll().then((result) => {
        var array = []
        for(var i = 0; i < result._delegate.items.length; i++){
          array.push(result._delegate.items[i]._location)
        }
        this.$data.uploadedFiles = array
        console.log(this.$data.uploadedFiles)
      })
    },
    viewGroup(item){
      this.selectedItem = item
      this.viewDialog = true;
    },
    deleteGroup(item) {
       firebase.firestore().collection("groups").doc(item.name).delete()
       this.groups = this.groups.filter(v => v !== item)
       firebase.firestore().collection("users").get().then((querySnapshot) => {
            querySnapshot.forEach((doc) => {
                if(doc.data().groups.includes(item.name)){
                firebase.firestore().collection("users").doc(doc.id).update({
                  groups: doc.data().groups.filter(v => v !== item.name)
                });
              }
            });
        });
    },
    createKeyPairings () {
      var rsa = new RSA();
      rsa.generateKeyPair(this.createGroup)
    },
    createGroup (keyPair) {
        firebase.firestore().collection("groups").doc(this.groupName)
          .set({
              name: this.groupName,
              author: firebase.auth().currentUser.email,
              members: this.members,
              publicKey: keyPair.publicKey
          })
       this.dialogCreateGroup = false
       var members = this.$data.members
       firebase.firestore().collection("users").get().then((querySnapshot) => {
            querySnapshot.forEach((doc) => {
                if(members.includes(doc.data().email) || doc.data().email === firebase.auth().currentUser.email){
                firebase.firestore().collection("users").doc(doc.id).update({
                  groups: firebase.firestore.FieldValue.arrayUnion(this.groupName)
                });
                firebase.firestore().collection("users").doc(doc.id).update({
                  key: firebase.firestore.FieldValue.arrayUnion(keyPair.privateKey)
                });
              }
            });
        });
      this.groups.push({
        name: this.groupName,
        author: firebase.auth().currentUser.email,
        members: this.members,
        publicKey: keyPair.publicKey
      })
      console.log(this.groups)
    },
    getUsers () {
        firebase.firestore().collection("users").get().then((querySnapshot) => {
        querySnapshot.forEach((doc) => {
            if(doc.data().email != firebase.auth().currentUser.email)
              this.$data.listOfUsers.push(doc.data().email);
        });
    })
    .catch((error) => {
        console.log("Error getting documents: ", error);
    });
      },
    getGroups() {
      firebase.firestore().collection("groups").get().then((querySnapshot) => {
        querySnapshot.forEach((doc) => {
          if(doc.data().members.includes(firebase.auth().currentUser.email) || doc.data().author === firebase.auth().currentUser.email)
            this.groups.push(doc.data())
            });
        });
    }
}
}
</script>
