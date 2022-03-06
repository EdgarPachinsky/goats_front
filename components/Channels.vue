<template>
  <div>
    <template v-if="channels && channels.length > 0">
      <h4>Channels list in workspace</h4>

      <b-row>
        <b-col cols="2" v-for="channel in channels" :key="channel._id">
          <div class="channel-container">
            <img
              class="user-avatar-small"
              :src="hostPrefix+'/'+channel.image"
              alt="">
            <p class="text-center margin-top-10">{{ channel.name }}</p>
            <hr>
            <p class="text-center">{{ channel.description }}</p>

            <b-button type="button" @click="deleteChannel(channel._id)"
                      class="margin-top-5 width-percent-100"
                      variant="danger">
              Delete channel
            </b-button>
          </div>
        </b-col>
      </b-row>
    </template>
  </div>
</template>

<script>
export default {
  name: "Channels",

  props: ['workspaceId'],

  methods: {
    deleteChannel(id){

      this.$axios.delete(`/channels/${id}`, {
        headers: {
          'Authorization': this.$auth.strategy.token.get()
        },
      }).then(({data}) => {

        this.fetchChannels()
      })
    },

    fetchChannels(){
      this.$axios.get(`/workspaces/workspace-channels/${this.workspaceId}`, {
        headers: {
          'Authorization': this.$auth.strategy.token.get()
        },
      }).then(({data}) => {
        this.channels = data
      })
    }
  },


  data() {
    return {
      hostPrefix: `http://localhost:3000/file`,
      channels: []
    }
  },

  mounted() {
  }
}
</script>

<style scoped>

</style>
