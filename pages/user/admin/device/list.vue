<template>

  <div>
    <b-modal
      id="modal-center"
      centered
      title="Add Device"
    >

      <b-form-group
        id="input-group-4" label="Name:" label-for="name">
        <b-form-input
          type="text"
          v-model="name"
          id="name"
          placeholder="Name of device"
          required
        ></b-form-input>
      </b-form-group>

      <b-form-group
        id="params" label="Params:" label-for="params">
        <span @click="clearAllParams()" v-if="params.length>0" class="clear-all-button">Clear all</span>
        <b-form-tags
          input-id="params"
          v-model="params"
          duplicate-tag-text="Duplicate parameter"></b-form-tags>
      </b-form-group>

      <b-form-group
        id="input-group-4" label="Number:" label-for="deviceNumber">
        <b-form-input
          type="text"
          v-model="deviceNumber"
          id="deviceNumber"
          placeholder="Number of device"
          required
        ></b-form-input>
      </b-form-group>

<!--      <b-form-group-->
<!--        id="params" label="Params:" label-for="params">-->
<!--        <b-form-file-->
<!--          @change="fileChange($event)"-->
<!--          v-model="image"-->
<!--          placeholder="Choose a file or drop it here..."-->
<!--          drop-placeholder="Drop file here..."-->
<!--        ></b-form-file>-->
<!--      </b-form-group>-->

      <template #modal-footer="{ save, cancel, close }">
        <!-- Emulate built in modal footer ok and cancel button actions -->
        <b-button size="sm" variant="success"
                  @click="saveDeviceAction(isEdit)">
          SAVE
        </b-button>
        <!-- Button with custom close trigger value -->
        <b-button size="sm" variant="outline-secondary" @click="hide('forget')">
          CLOSE
        </b-button>
      </template>
    </b-modal>

    <b-button
      type="button"
      variant="danger"
      class="margin-bottom-15"
      to="/user/cabinet">Back to cabinet</b-button>

    <b-button
      type="button"
      variant="danger"
      class="margin-bottom-15"
      v-b-modal.modal-center
    >Add Device</b-button>

    <b-row>
      <b-col sm="12" md="12" lg="12">

        <b-table
          :per-page="perPage"
          :current-page="currentPage"
          striped hover :items="devices" fixed bordered :fields="fields" :busy="loadedDevices">
          <template
            #cell(actions)="data">
            <b-button
              type="button"
              variant="primary"
              class="margin-bottom-15"
              size="sm"
              v-b-modal.modal-center
              @click="loadCurrentDeviceAction(data.item._id)"
            >EDIT
            </b-button>
            <b-button
              type="button"
              variant="outline-danger"
              class="margin-bottom-15"
              size="sm"
              :disabled="data.item._id===$auth.user._id"
              @click="deleteDeviceAction(data.item._id)"
            >DELETE
            </b-button>

          </template>
          <template #table-busy>
            <div class="text-center text-danger my-2">
              <b-spinner class="align-middle"></b-spinner>
              <strong>Loading...</strong>
            </div>
          </template>
        </b-table>

        <b-pagination
          v-model="currentPage"
          :total-rows="devices.length"
          :per-page="perPage"
          aria-controls="my-table"
        ></b-pagination>

      </b-col>
    </b-row>
  </div>
</template>

<script>
import {mapActions, mapState} from "vuex";

export default {

  async asyncData({ store }){

  },

  name: "list",

  middleware: 'auth',

  computed:{
    ...mapState('device', ['devices','loadedDevices', 'currentDevice'])
  },


  components: {},

  data() {
    return {
      fields: [
        { key: '#', label: '#',
          thStyle: {
            backgroundColor: '#3eef33',
            width: '34px'
          },
        }, 'name', 'params',

        { key: 'deviceNumber', label: 'Device Number' },
        { key: 'actions', label: 'Actions' }
      ],

      perPage: 10,
      currentPage: 1,

      isEdit: false,

      _id:null,
      name:null,
      params:[],
      image: null,
      deviceNumber: null,
    }
  },

  methods:{
    ...mapActions('device', ['loadDevices', 'saveDevice', 'deleteDevice', 'loadCurrentDevice']),

    fileChange(event) {

      if (!event.target.files)
        return

      this.image = event.target.files[0]
      // this.avatarImagePrev = URL.createObjectURL(this.avatarImage)
    },

    async saveDeviceAction(){

      await this.saveDevice({
        ...this.isEdit && {
          _id: this._id
        },
        'name': this.name,
        'parameters': this.params,
        'deviceNumber': this.deviceNumber,
      })

      this.loadDevices()
    },

    clearAllParams(){
      this.params = []
    },

    async deleteDeviceAction(id){
      await this.deleteDevice(id)
      this.loadDevices()
    },

    async loadCurrentDeviceAction(id){
      await this.loadCurrentDevice(id)

      this.isEdit = true

      this._id = this.currentDevice?._id?this.currentDevice._id:null
      this.name = this.currentDevice?.name?this.currentDevice.name:null
      this.params = this.currentDevice?.parameters?this.currentDevice.parameters:[]
    },
  },


  async mounted() {
    $nuxt.$emit('setPageTitle', {title: 'Users List'})
    const admin = await this.$store.dispatch('user/isAdmin', this.$auth.user)
    if(!admin)
      await this.$router.push('/user/cabinet')

    this.loadDevices()
  }
}

</script>

<style scoped>

</style>
