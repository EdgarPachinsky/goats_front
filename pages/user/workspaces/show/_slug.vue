<template>
  <div>
    <div v-if="wsId" class="workspace-info">
      <div class="workspace-image">
        <img
          class="ws-image"
          :src="hostPrefix+'/'+image"
          alt="">
      </div>

      <div>
        <div class="workspace-details">
          {{ name }}
        </div>
        <div class="workspace-details">
          {{ description }}
        </div>
      </div>

    </div>

    <b-button
      type="button"
      variant="danger"
      class="margin-top-20"
      to="/user/workspaces/actions/list">Back to list
    </b-button>

    <b-button
      type="button"
      variant="danger"
      class="margin-top-20"
      @click="$refs.channels.fetchChannels()"
    >Get channel list
    </b-button>

    <b-button
      type="button"
      variant="danger"
      class="margin-top-20"
      @click="addChannelActive=!addChannelActive"
    >Add channel
    </b-button>

    <b-button
      type="button"
      variant="outline-primary"
      class="margin-top-20"
      @click="addUserActive=!addUserActive"
    >Invite user
    </b-button>
    <b-button
      type="button"
      variant="outline-primary"
      class="margin-top-20"
    >Coworkers
    </b-button>

    <div class="add-container" v-if="addChannelActive">
      <b-row>
        <b-col cols="4">
          <div class="inner">
            <b-form-group id="input-group-1" label="Name:" label-for="name">
              <b-form-input
                id="name"
                type="text"
                placeholder="Enter channel name"
                required
                v-model="newChannel.name"
              ></b-form-input>
            </b-form-group>

            <b-form-group id="input-group-2" label="Description:" label-for="description">
              <b-form-input
                id="description"
                type="text"
                placeholder="Enter channel description"
                required
                v-model="newChannel.description"
              ></b-form-input>
            </b-form-group>

            <b-form-group
              description="Image"
              id="input-group-3" label="Image:" label-for="image">
              <b-form-file
                id="image"
                class="margin-bottom-6"
                placeholder="Choose a file or drop it here..."
                drop-placeholder="Drop file here..."
                v-model="newChannel.image"
                @change="createImageUrl($event)"
              ></b-form-file>
            </b-form-group>

            <b-button
              type="button"
              variant="danger"
              class="margin-top-20"
              @click="addChannel"
            >Add
            </b-button>
          </div>
        </b-col>
      </b-row>
    </div>

    <div class="add-container" v-if="addUserActive">
      <b-row>
        <b-col cols="4">
          <div class="inner">

            <b-form-input
              id="name"
              type="text"
              placeholder="Enter username"
              required
              v-model="searchUserUsername"
            ></b-form-input>

            <b-button
              type="button"
              variant="danger"
              class="margin-top-20"
              @click="sendInvitation"
            >Send invitation
            </b-button>
          </div>
        </b-col>
      </b-row>
    </div>

    <hr>

    <Channels ref="channels" :workspaceId="wsId"/>

  </div>
</template>

<script>
import Channels from "../../../../components/Channels";

export default {
  async asyncData({params}) {
    const slug = params.slug // When calling /abc the slug will be "abc"
    return {slug}
  },

  components: {
    Channels
  },

  data() {
    return {
      addChannelActive: false,
      addUserActive: false,
      wsImage: null,
      wsId: null,
      hostPrefix: `http://91.103.29.205:3000/file`,
      name: null,
      description: null,
      image: null,
      searchUserUsername:null,

      newChannel: {
        name:null,
        description:null,
        image:null,
      }
    }
  },

  methods: {
    createImageUrl(event) {

      if (!event.target.files)
        return

      this.newChannel.image = event.target.files[0]
    },

    addChannel(){

      let form = new FormData()
      form.append('name', this.newChannel.name)
      form.append('description', this.newChannel.description)
      form.append('workspace', this.wsId)
      form.append('image', this.newChannel.image)

      this.$axios.post(`/channels`, form,{
        headers: {
          'Authorization': this.$auth.strategy.token.get()
        },
      }).then(({data}) => {
        this.$nuxt.$emit('setMessage', {type: data.status, message: data.message})

        this.$refs.channels.fetchChannels()
      })
    },

    sendInvitation(){

      this.$axios.post(`/workspace-invitations`,
        {
          username: this.searchUserUsername,
          workspace: this.wsId,
        },
        {
        headers: {
          'Authorization': this.$auth.strategy.token.get()
        },
      }).then(({data}) => {
        this.$nuxt.$emit('setMessage', {type: data.status, message: data.message})
      })
    }
  },

  mounted() {
    $nuxt.$emit('setPageTitle', {title: `Showing ${this.slug}`})

    this.$axios.get(`/workspaces/${this.slug}`, {
      headers: {
        'Authorization': this.$auth.strategy.token.get()
      },
    }).then(({data}) => {
      if (data.data === null) {
        data = null
        this.$nuxt.$emit('setMessage', {type: 'error', message: 'workspace not found'})
        this.$router.push('/user/workspaces/actions/list')
      }
      console.log(data.data)
      this.name = data.data.name
      this.description = data.data.description
      this.wsId = data.data._id
      this.image = data.data.image
    })
  }
}
</script>
