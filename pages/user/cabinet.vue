<template>
  <div>

    <template
      v-if="isAdmin">

      <b-button
        type="button"
        variant="danger"
        class="margin-bottom-15"
        to="/user/admin/users/list">User List</b-button>
      <b-button
        type="button"
        variant="danger"
        class="margin-bottom-15"
        to="/user/admin/device/list">Device List</b-button>

    </template>

      <b-button
        type="button"
        variant="danger"
        class="margin-bottom-15"
        to="/user/my-device/list">My Devices</b-button>

    <b-row>
      <b-col sm="12" md="4" lg="4">

        <div class="user-info-card">
          <div>
            <b-card
              :title="'@_'+$auth.user.username || $auth.user.fullName"
              img-alt="Image"
              img-top
              style="max-width: 20rem;"
              class="mb-2 text-center user-card"
            >
              <b-card-text>
                Full Name | {{ $auth.user.fullName }}
              </b-card-text>

              <b-card-text>
                Email | {{ $auth.user.email }}
              </b-card-text>

              <b-card-text>
                Roles | {{ roles }}
              </b-card-text>
            </b-card>
          </div>
          <hr>

<!--          <h3>Invitations list</h3>-->
<!--          <div class="invitation-container margin-bottom-15">-->
<!--            <div v-for="invitation in invitations" :key="invitation._id" class="invitation-inner">-->
<!--              <p>From: {{invitation.from.firstName}}</p>-->
<!--              <p>Workspace: {{invitation.workspace.name}}</p>-->
<!--              <b-button-->
<!--                type="button"-->
<!--                variant="success"-->
<!--                class=""-->
<!--                >-->
<!--                Accept-->
<!--              </b-button>-->
<!--            </div>-->
<!--          </div>-->
        </div>


      </b-col>

      <b-col sm="12" md="8" lg="8">

<!--        <b-form-->
<!--          @submit="onSubmit"-->
<!--          @reset="onReset"-->
<!--        >-->
<!--          <b-row>-->

<!--            <b-col sm="12" md="6" lg="6">-->

<!--              <b-form-group id="input-group-1" label="Email address:" label-for="email"-->
<!--                            description="We'll never share your email with anyone else."-->
<!--              >-->
<!--                <b-form-input-->
<!--                  id="email"-->
<!--                  v-model="form.email"-->
<!--                  type="email"-->
<!--                  placeholder="Enter email"-->
<!--                  required-->
<!--                ></b-form-input>-->
<!--              </b-form-group>-->

<!--              <b-form-group-->
<!--                description="Example. John"-->
<!--                id="input-group-3" label="First Name:" label-for="firstName">-->
<!--                <b-form-input-->
<!--                  id="firstName"-->
<!--                  v-model="form.firstName"-->
<!--                  type="text"-->
<!--                  placeholder="Enter first name"-->
<!--                  required-->
<!--                ></b-form-input>-->
<!--              </b-form-group>-->

<!--              <b-form-group-->
<!--                description="Example. Doe"-->
<!--                id="input-group-2" label="Last Name:" label-for="lastName">-->
<!--                <b-form-input-->
<!--                  id="lastName"-->
<!--                  v-model="form.lastName"-->
<!--                  type="text"-->
<!--                  placeholder="Enter last name"-->
<!--                  required-->
<!--                ></b-form-input>-->
<!--              </b-form-group>-->


<!--              <b-form-group-->
<!--                description="Example. 18"-->
<!--                id="input-group-3" label="Age:" label-for="age">-->
<!--                <b-form-input-->
<!--                  id="age"-->
<!--                  v-model="form.age"-->
<!--                  type="number"-->
<!--                  placeholder="Enter last name"-->
<!--                  required-->
<!--                ></b-form-input>-->
<!--              </b-form-group>-->


<!--            </b-col>-->

<!--            <b-col sm="12" md="6" lg="6">-->
<!--              <b-form-group-->
<!--                description="Minimum length of 8"-->
<!--                id="input-group-4" label="Username:" label-for="username">-->
<!--                <b-form-input-->
<!--                  id="username"-->
<!--                  v-model="form.username"-->
<!--                  placeholder="Enter your username"-->
<!--                  required-->
<!--                ></b-form-input>-->
<!--              </b-form-group>-->


<!--              <b-form-group-->
<!--                description="Avatar"-->
<!--                id="input-group-6" label="Avatar:" label-for="avatar">-->
<!--                <b-form-file-->
<!--                  id="avatar"-->
<!--                  @change="createImageUrl($event)"-->
<!--                  v-model="form.avatar"-->
<!--                  class="margin-bottom-6"-->
<!--                  placeholder="Choose a file or drop it here..."-->
<!--                  drop-placeholder="Drop file here..."-->
<!--                ></b-form-file>-->
<!--                <div class="mt-3">Selected file: {{ form.avatar ? form.avatar.name : '' }}</div>-->
<!--              </b-form-group>-->
<!--              <div v-if="avatarImagePrev" class="avatar-image-preview">-->
<!--                <img :src="avatarImagePrev" alt="Token Image">-->
<!--              </div>-->
<!--            </b-col>-->

<!--            <b-col cols="12">-->

<!--              <b-form-group id="input-group-4" v-slot="{ ariaDescribedby }">-->
<!--                <b-form-checkbox-group-->
<!--                  v-model="form.checked"-->
<!--                  id="checkboxes-4"-->
<!--                  :aria-describedby="ariaDescribedby"-->
<!--                >-->

<!--                  &lt;!&ndash;            <b-form-checkbox value="agreed">&ndash;&gt;-->
<!--                  &lt;!&ndash;              Agreed with therms of use&ndash;&gt;-->

<!--                  &lt;!&ndash;              <nuxt-link class="custom-link" to="/">&ndash;&gt;-->
<!--                  &lt;!&ndash;                <b-icon icon="link45deg"></b-icon>&ndash;&gt;-->
<!--                  &lt;!&ndash;              </nuxt-link>&ndash;&gt;-->
<!--                  &lt;!&ndash;            </b-form-checkbox>&ndash;&gt;-->
<!--                </b-form-checkbox-group>-->
<!--              </b-form-group>-->
<!--              <hr>-->

<!--              <b-button type="submit" variant="">Submit</b-button>-->
<!--            </b-col>-->
<!--          </b-row>-->
<!--        </b-form>-->
      </b-col>
    </b-row>
  </div>
</template>

<script>

export default {
  name: "cabinet",

  middleware: 'auth',

  components: {},


  data() {
    return {
      timestamp: "",
      roles: null,
      isAdmin: false,
      // userAvatar: `http://91.103.29.205:3000/file/${this.$auth.user.avatar ? this.$auth.user.avatar : "placeholder.png"}`,
      // avatarImagePrev: null,
      // avatarImage: null,
      // invitations: [],
      // form: {
      //   email: this.$auth.user.email,
      //   // firstName: this.$auth.user.firstName,
      //   // lastName: this.$auth.user.lastName,
      //   // fullName: this.$auth.user.lastName,
      //   username: this.$auth.user.username,
      //   password: null,
      //   age: this.$auth.user.age,
      //   avatar: null,
      //   checked: [],
      // },
    }
  },

  methods: {
    // createImageUrl(event) {
    //
    //   if (!event.target.files)
    //     return
    //
    //   this.avatarImage = event.target.files[0]
    //   this.avatarImagePrev = URL.createObjectURL(this.avatarImage)
    // },
    //

    // fetchUserInvitations(){
    //
    //   this.$axios.post(`/workspace-invitations/user-invitations`,{
    //     headers: {
    //       'Authorization': this.$auth.strategy.token.get()
    //     },
    //   }).then(({data}) => {
    //
    //     //emmit message to default layout
    //     this.$nuxt.$emit('setMessage', {type: data.status, message: data.status})
    //
    //     this.invitations = data
    //   })
    // },

    // onReset(event) {
    //   event.preventDefault()
    //   // Reset our form values
    //   this.form.email =  this.$auth.user.email
    //   this.form.firstName = this.$auth.user.firstName
    //   this.form.lastName = this.$auth.user.lastName
    //   this.form.age =  this.$auth.user.age
    //   this.form.username = this.$auth.user.username
    //   this.form.password = null
    //   this.form.avatar = null
    //   this.avatarImage = null
    //   this.avatarImagePrev = null
    // },

    // async onSubmit(event) {
    //   event.preventDefault()
    //
    //   // do some validation staff
    //   // if (!this.validateEmail(this.form.email)) {
    //   //   this.$nuxt.$emit('setMessage', {type: 'error', message: 'wrong email entered'})
    //   //   return
    //   // }
    //   //
    //   // if (!this.validateUserName(this.form.username)) {
    //   //   this.$nuxt.$emit('setMessage', {type: 'error', message: 'username must be longer then 8 characters'})
    //   //   return
    //   // }
    //   //
    //   // if (!this.validatePassword(this.form.password)) {
    //   //   this.$nuxt.$emit('setMessage', {type: 'error', message: 'password must be longer then 8 characters'})
    //   //   return
    //   // }
    //
    //   //create new form and append fields
    //   let form = new FormData()
    //   this.form.email?form.append('email', this.form.email):""
    //   this.form.firstName?form.append('firstName', this.form.firstName):""
    //   this.form.lastName?form.append('lastName', this.form.lastName):""
    //   this.form.age?form.append('age', parseInt(this.form.age)):""
    //   this.form.username?form.append('username', this.form.username):""
    //   this.avatarImage?form.append('avatar', this.avatarImage):""
    //
    //   //request to back end to save user
    //   this.$axios.patch(`/users/${this.$auth.user._id}`, form).then(({data}) => {
    //
    //     //emmit message to default layout
    //     this.$nuxt.$emit('setMessage', {type: data.status, message: data.status})
    //
    //     console.log(data)
    //   })
    // },
  },

  async mounted() {
    $nuxt.$emit('setPageTitle', {title: 'Personal Cabinet'})
    this.roles = await this.$store.dispatch('user/extractRole', this.$auth.user)
    this.isAdmin = await this.$store.dispatch('user/isAdmin', this.$auth.user)
  }
}
</script>

<style scoped>

</style>
