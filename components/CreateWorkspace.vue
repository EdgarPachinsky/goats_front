<template>
  <div>
    <template v-if="workspaces && workspaces.length === 0">
      <h5>No any Workspaces</h5>
    </template>


    <template v-else>

      <b-row>

        <b-col cols="3" v-for="workspace in workspaces" :key="workspace._id">
          <div class="mint-token-prev-container no-center">
            <div class="mint-token-image">
              <img v-if="workspace.image"
                   :src="'http://localhost:3000/file/'+workspace.image"
                   alt="Workspace Image">
              <div v-else>Image preview</div>
            </div>
            <div class="text-center">
              <h5 class="margin-bottom-5 margin-top-5">{{ workspace.name ? workspace.name : "No Name" }}</h5>
              <p class="margin-bottom-5 margin-top-5">{{ workspace.description ? workspace.description : "-" }}</p>
            </div>
            <hr>
            <div class="actions">
              <b-button type="button" variant="">
                Open WS
              </b-button>
            </div>
          </div>
        </b-col>
      </b-row>
    </template>
  </div>
</template>

<script>
import {mapState, mapMutations, mapActions} from "vuex";

export default {
  name: "CreateWorkspace",

  computed: {
    ...mapState('load-workspaces', ['workspaces']),
  },

  data() {
    return {}
  },

  methods: {

    ...mapActions('load-workspaces', ['loadWorkspaces']),

    createWallet() {
      console.log('WALLET CREATE REQUEST')

      this.$axios.post('/wallet/save', {
        headers: {
          'Authorization': this.$auth.strategy.token.get()
        },
      }, {}).then(({data}) => {
        this.$nuxt.$emit('setMessage', {type: data.status, message: data.message})
        if (data.status === 'success') {
          this.wallet = data.wallet
        }
      })
    },
  },

  async mounted() {
    this.loadWorkspaces()
    // this.$store.dispatch('load-workspaces/loadWorkspaces')
  },
}
</script>

<style scoped>

</style>
