<template>
<v-card height="100px" width="1200px">
    <div v-if="!input">
    <v-btn icon height="100px" width="100px">
    <v-icon x-large>mdi-folder-plus</v-icon>
    </v-btn>
    </div>
</v-card>
</template>

<script>
export default {
  name: 'Group',
  props: {
        'group': {
            type: Object
        }
    },
  data () {
    return {
        input: this.group
    }
  }
}
</script>
