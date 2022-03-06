<template>
  <div>
    <b-button
      type="button"
      variant="danger"
      class="margin-bottom-15"
      to="/user/cabinet">Back to cabinet
    </b-button>

    <b-button
      type="button"
      variant="danger"
      class="margin-bottom-15"
      to="/user/my-device/list">My Devices</b-button>

    <b-modal id="modal-1" title="History for animal">
      <template v-if="currentAnimalIdForGraphic">
        Animal Id -> {{currentAnimalIdForGraphic}} <br>

        Chars here
      </template>
      <template v-else>
        Please select animal id
      </template>

    </b-modal>

    <b-row>
      <b-col sm="12" md="10" lg="10">
        <b-form-input
          v-model="currentAnimalIdForGraphic"
          id="animalId"
          placeholder="Animal ID"
          type="text"
        ></b-form-input>
      </b-col>

      <b-col sm="12" md="2" lg="2">
        <b-button class="w-100" v-b-modal.modal-1>Time Graphic</b-button>
      </b-col>
    </b-row>
    <hr>
    <b-row>
      <b-col sm="12" md="12" lg="12">
        <div class="main-t-body">
          <b-table
            bordered
            striped
            hover
            :items="deviceHistory"
            :fields="fields"
            :busy="loadedHistory"
            fixed
            :per-page="perPage"
            :current-page="currentPage"
          >
            <template #table-busy>
              <div class="text-center text-danger my-2">
                <b-spinner class="align-middle"></b-spinner>
                <strong>Loading...</strong>
              </div>
            </template>
          </b-table>

          <b-pagination
            v-model="currentPage"
            :total-rows="deviceHistory.length"
            :per-page="perPage"
            aria-controls="my-table"
          ></b-pagination>
        </div>
      </b-col>
    </b-row>
  </div>
</template>

<script>

import {mapActions, mapState} from "vuex";

export default {
  async asyncData({params}) {
    const slug = params.slug // When calling /abc the slug will be "abc"
    return {slug}
  },

  components: {},

  computed: {
    ...mapState('device', ['deviceHistory', 'loadedHistory'])
  },

  data() {
    return {
      fields: [
        {
          key: '#', label: '#',
          thStyle: {
            backgroundColor: '#3eef33',
            width: '34px'
          },
        },
        {key: 'animalId', label: 'Animal ID'},
        'weight',
        {key: 'createdAt', label: 'At'},

      ],

      currentAnimalIdForGraphic:null,
      perPage: 10,
      currentPage: 1,
    }
  },

  methods: {
    ...mapActions('device', ['loadDeviceHistory'])
  },

  async mounted() {
    $nuxt.$emit('setPageTitle', {title: `History for device (${this.slug})`})

    await this.loadDeviceHistory(this.slug)
  }
}
</script>
