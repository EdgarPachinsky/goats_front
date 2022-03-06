<template>

  <div>
    <b-button
      type="button"
      variant="danger"
      class="margin-bottom-15"
      to="/user/cabinet">Back to cabinet
    </b-button>


    <b-row>
      <b-col sm="12" md="4" lg="3" v-for="device in currentUserDevices" :key="device._id">
        <div class="channel-container">
          <img
            class="user-avatar-small"
            src="/images/icons/device.png"
            alt=""
          >
          <p class="text-center margin-top-10">{{ device.name }}</p>
          <hr>
<!--          <p class="text-center">{{ device.parameters }}</p>-->

          <b-button
            type="button"
            class="margin-top-5 width-percent-100"
            variant="outline-danger"
            :to="`/user/device-history/show/${device._id}`"
          >
            Show history
          </b-button>
        </div>
      </b-col>
    </b-row>
  </div>
</template>

<script>
import {mapActions, mapState} from "vuex";

export default {

  name: "list",

  middleware: 'auth',

  computed: {
    ...mapState('user', ['currentUserDevices'])
  },

  components: {},

  data() {
    return {}
  },

  methods: {
    ...mapActions('user', ['loadCurrentUserDevices'])
  },


  async mounted() {
    $nuxt.$emit('setPageTitle', {title: 'My Devices'})
    await this.loadCurrentUserDevices()
  }
}

</script>

<style scoped>

</style>
