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
                        label="Select"
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
                    @click="createGroup()"
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
               <v-dialog v-model="deleteGroupDialog" max-width="500px">
              <v-card>
                <v-card-title class="headline">Are you sure you want to delete this item?</v-card-title>
                <v-card-actions>
                  <v-spacer></v-spacer>
                  <v-btn color="blue darken-1" text @click="closeDeleteGroup()">Cancel</v-btn>
                  <v-btn color="blue darken-1" text @click="deleteGroupConfirm()">OK</v-btn>
                  <v-spacer></v-spacer>
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
                <v-icon small class="mr-2" @click="viewGroup(item)">
                  mdi-pencil
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
          text: 'Edit / Delete',
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
    createGroup () {
       firebase.firestore().collection("groups").doc(this.groupName)
          .set({
              name: this.groupName,
              author: firebase.auth().currentUser.email,
              members: this.members,
              files: []
          })
       this.dialogCreateGroup = false
       var members = this.$data.members
       firebase.firestore().collection("users").get().then((querySnapshot) => {
            querySnapshot.forEach((doc) => {
                if(members.includes(doc.data().email) || doc.data().email === firebase.auth().currentUser.email){
                firebase.firestore().collection("users").doc(doc.id).update({
                  groups: firebase.firestore.FieldValue.arrayUnion(this.groupName)
                });
              }
            });
        });
      this.groups.push({
        name: this.groupName,
        author: firebase.auth().currentUser.email,
        members: this.members,
        files: []
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
             this.groups.push(doc.data())
              });
         });
      }
    }
}
</script>
