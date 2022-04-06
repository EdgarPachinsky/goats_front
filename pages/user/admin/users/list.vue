<template>

  <div>
    <b-modal
      v-model="show"
      id="modal-center"
      centered
      title="Add user"
    >

      <b-form-group
        id="input-group-4" label="Full Name:" label-for="fullName">
        <b-form-input
          type="text"
          v-model="fullName"
          id="fullName"
          placeholder="Full name"
          required
        ></b-form-input>
      </b-form-group>

      <b-form-group
        id="input-group-4" label="Username:" label-for="username">
        <b-form-input
          id="username"
          v-model="username"
          type="text"
          placeholder="Username"
          required
        ></b-form-input>
      </b-form-group>

      <b-form-group
        id="input-group-4" label="Email:" label-for="email">
        <b-form-input
          id="email"
          v-model="email"
          type="email"
          placeholder="Email"
          required
        ></b-form-input>
      </b-form-group>

      <b-form-group
        v-if="!isEdit"
        id="input-group-4" label="Password:" label-for="password">
        <b-form-input
          v-model="password"
          id="password"
          type="password"
          placeholder="Password"
          required
        ></b-form-input>
      </b-form-group>

      <b-form-group
        id="input-group-4" label="Devices:" label-for="device">
        <b-form-select
          id="device"
          v-model="selected_devices"
          :options="selectValue_device"
          multiple :select-size="4"></b-form-select>
      </b-form-group>

      <b-form-group
        id="input-group-4" label="Roles:" label-for="role">
        <b-form-select
          id="role"
          v-model="selected_roles"
          :options="selectValue_role"
          multiple :select-size="4"></b-form-select>
      </b-form-group>

      <template #modal-footer="{ save, cancel, close }">
        <!-- Emulate built in modal footer ok and cancel button actions -->
        <b-button size="sm" variant="success"
                  @click="saveUserAction(isEdit)">
          SAVE
        </b-button>
        <!-- Button with custom close trigger value -->
        <b-button size="sm" variant="outline-secondary" @click="show=false">
          CLOSE
        </b-button>
      </template>
    </b-modal>

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
      v-b-modal.modal-center
      @click="loadSelectValues"
    >Add User
    </b-button>

    <b-row>
      <b-col sm="12" md="12" lg="12">
        <div class="main-t-body">
          <b-table
            bordered
            striped
            hover
            :items="users"
            :fields="fields"
            :busy="loadedUsers"
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


            <template #cell(actions)="data">
              <b-button
                type="button"
                variant="primary"
                class="margin-bottom-15"
                size="sm"
                v-b-modal.modal-center
                @click="loadCurrentUser(data.item._id)"
              >EDIT <b-icon icon="pencil" aria-hidden="true"></b-icon>
              </b-button>
              <b-button
                type="button"
                variant="outline-danger"
                class="margin-bottom-15"
                size="sm"
                :disabled="data.item._id===$auth.user._id"
                @click="deleteUserAction(data.item._id)"
              >
                DELETE <b-icon icon="trash" aria-hidden="true"></b-icon>
              </b-button>

            </template>

            <template #cell(isDisabled)="data">
              <b-form-checkbox
                :disabled="data.item._id===$auth.user._id"
                @change="changeStatus(data.item._id)"
                :checked="!data.item.isDisabled"
                switch
                size="sm"
              >
                <div
                  :class="data.item.isDisabled?'disabled':'active'"
                  class="user-list-status"
                >
                  {{ data.item.isDisabled?"Inactive":"Active" }}
                </div>
                <div>
                  {{ data.item._id===$auth.user._id?"You can not disable your account":"" }}
                </div>
              </b-form-checkbox>
            </template>
          </b-table>

          <b-pagination
            v-model="currentPage"
            :total-rows="users.length"
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

  name: "list",

  middleware: 'auth',

  computed: {
    ...mapState('user', ['users', 'loadedUsers', 'currentUser']),
    ...mapState('device', ['devices', 'selectValue_device', 'currentUserDevices']),
    ...mapState('role', ['roles', 'selectValue_role']),
  },

  components: {},

  data() {
    return {
      show: false,
      toastCount: 0,
      fields: [
        { key: '#', label: '#',
          thStyle: {
            backgroundColor: '#3eef33',
            width: '34px'
          },
        },
        'fullName', 'username', 'email',
        { key: 'devices', label: 'Devices',
          thStyle: {
            'word-break': 'break-word'
          },
        },
        'roles',
        { key: 'actions', label: 'Actions' },
        { key: 'isDisabled', label: 'Status' },
      ],

      perPage: 10,
      currentPage: 1,

      _id:this.currentUser?._id?this.currentUser._id:null,
      fullName: this.currentUser?.fullName?this.currentUser.fullName:null,
      username: this.currentUser?.username?this.currentUser.username:null,
      email: this.currentUser?.email?this.currentUser.email:null,
      password: this.currentUser?.password?this.currentUser.password:null,
      isDisabled: this.currentUser?.isDisabled?this.currentUser.isDisabled:false,
      selected_devices: this.currentUser?.selected_devices?this.currentUser.selected_devices:[],
      selected_roles: this.currentUser?.selected_roles?this.currentUser.selected_roles:[],

      showPassword:true,
      isEdit: false
    }
  },

  methods: {
    ...mapActions('user', [
      'loadUser', 'saveUser', 'deleteUser', 'changeUserStatus', 'loadCurrentUserData','clearCurrentUser'
    ]),
    ...mapActions('device', ['loadDevices', 'transformForSelect_device']),
    ...mapActions('role', ['transformForSelect_role']),

    reloadPage(){
      window.location.reload(true);
    },

    makeToast(append = false, message) {
      this.toastCount++
      this.$bvToast.toast(message, {
        title: 'Action Done',
        autoHideDelay: 4000,
        appendToast: append
      })
    },

    hideModal() {
      this.$root.$emit('bv::hide::modal', 'modal-1', '#btnShow')
    },

    loadSelectValues() {
      this.isEdit = false
      this.clearCurrentUser();
      this.show = true
      this.fullName = null
      this.username = null
      this.email = null
      this.password = null
      this.isDisabled = false
      this.selected_devices = []
      this.selected_roles = []

      this.transformForSelect_device();
      this.transformForSelect_role();
    },

    async loadCurrentUser(userId) {
      await this.loadCurrentUserData(userId)
      this.isEdit = true

      this._id = this.currentUser?._id?this.currentUser._id:null
      this.fullName = this.currentUser?.fullName?this.currentUser.fullName:null
      this.username = this.currentUser?.username?this.currentUser.username:null
      this.email = this.currentUser?.email?this.currentUser.email:null
      this.isDisabled = this.currentUser?.isDisabled?this.currentUser.isDisabled:false

      await this.transformForSelect_device();
      await this.transformForSelect_role();

      this.selected_devices = this.currentUser?.device?this.currentUser.device:[]
      this.selected_roles = this.currentUser?.role?this.currentUser.role:[]
    },

    async saveUserAction(){

      this.saveUser({
        ...this.isEdit && {
          _id: this._id
        },
        'fullName': this.fullName,
        'username': this.username,
        'email': this.email,
        'password': this.password,
        'isDisabled': this.isDisabled,
        'role': this.selected_roles,
        'device': this.selected_devices
      }).then((data) => {

        let message = ``
        if(data && data.result) {
          let result = data.result
          message = result.message
        }

        this.makeToast(false, message)
        this.show = false
        this.loadUser()
      })
    },

    async deleteUserAction(userId){
      this.deleteUser(userId).then((data) => {
        this.makeToast(false, `User deleted`)
        this.loadUser()
      })
    },

    async changeStatus(userId){
      this.changeUserStatus(userId)
      .then(() => {

        this.makeToast(false, `Status Changed!`)
        this.loadUser()
      })
    }
  },

  async mounted() {
    $nuxt.$emit('setPageTitle', {title: 'Users List'})
    const admin = await this.$store.dispatch('user/isAdmin', this.$auth.user)
    if (!admin)
      await this.$router.push('/user/cabinet')

    this.loadUser()
  }
}

</script>

<style scoped>

</style>
