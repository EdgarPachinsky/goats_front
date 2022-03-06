<template>
  <b-form
    @submit="onSubmit"
    @reset="onReset"
  >
    <b-row>
      <b-col sm="12" md="6" lg="6">

        <b-form-group id="input-group-1" label="Email address:" label-for="email"
                      description="We'll never share your email with anyone else."
        >
          <b-form-input
            id="email"
            v-model="form.email"
            type="email"
            placeholder="Enter email"
            required
          ></b-form-input>
        </b-form-group>

        <b-form-group
          description="Example. John"
          id="input-group-3" label="First Name:" label-for="firstName">
          <b-form-input
            id="firstName"
            v-model="form.firstName"
            type="text"
            placeholder="Enter first name"
            required
          ></b-form-input>
        </b-form-group>

        <b-form-group
          description="Example. Doe"
          id="input-group-2" label="Last Name:" label-for="lastName">
          <b-form-input
            id="lastName"
            v-model="form.lastName"
            type="text"
            placeholder="Enter last name"
            required
          ></b-form-input>
        </b-form-group>


        <b-form-group
          description="Example. 18"
          id="input-group-3" label="Age:" label-for="age">
          <b-form-input
            id="age"
            v-model="form.age"
            type="number"
            placeholder="Enter last name"
            required
          ></b-form-input>
        </b-form-group>


      </b-col>

      <b-col sm="12" md="6" lg="6">
        <b-form-group
          description="Minimum length of 8"
          id="input-group-4" label="Username:" label-for="username">
          <b-form-input
            id="username"
            v-model="form.username"
            placeholder="Enter your username"
            required
          ></b-form-input>
        </b-form-group>

        <b-form-group
          description="Minimum length of 8"
          id="input-group-5" label="Password:" label-for="password">
          <b-form-input
            id="password"
            v-model="form.password"
            placeholder="Enter password"
            type="password"
            required
          ></b-form-input>
        </b-form-group>


<!--        <b-form-group-->
<!--          description="Avatar"-->
<!--          id="input-group-6" label="Avatar:" label-for="avatar">-->
<!--          <b-form-file-->
<!--            id="avatar"-->
<!--            @change="createImageUrl($event)"-->
<!--            v-model="form.avatar"-->
<!--            class="margin-bottom-6"-->
<!--            placeholder="Choose a file or drop it here..."-->
<!--            drop-placeholder="Drop file here..."-->
<!--          ></b-form-file>-->
<!--          <div class="mt-3">Selected file: {{ form.avatar ? form.avatar.name : '' }}</div>-->
<!--        </b-form-group>-->
<!--        <div v-if="avatarImagePrev" class="avatar-image-preview">-->
<!--          <img :src="avatarImagePrev" alt="Token Image">-->
<!--        </div>-->
      </b-col>

      <b-col cols="12">

        <b-form-group id="input-group-4" v-slot="{ ariaDescribedby }">
          <b-form-checkbox-group
            v-model="form.checked"
            id="checkboxes-4"
            :aria-describedby="ariaDescribedby"
          >


<!--            <b-form-checkbox value="agreed">-->
<!--              Agreed with therms of use-->

<!--              <nuxt-link class="custom-link" to="/">-->
<!--                <b-icon icon="link45deg"></b-icon>-->
<!--              </nuxt-link>-->
<!--            </b-form-checkbox>-->
          </b-form-checkbox-group>
        </b-form-group>
        <hr>

        <b-button type="submit" variant="">Submit</b-button>
        <b-button type="reset" variant="danger" @click="onReset">Reset</b-button>
      </b-col>
    </b-row>
  </b-form>
</template>

<script>
export default {
  name: "login",

  auth: 'guest',

  data() {
    return {
      // avatarImagePrev: null,
      // avatarImage: null,
      form: {
        email: '',
        firstName: '',
        lastName: '',
        username: '',
        password: null,
        age: null,
        // avatar: null,
        checked: [],
      },
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

    validateEmail(mail) {
      return /^\w+([\.-]?\w+)*@\w+([\.-]?\w+)*(\.\w{2,3})+$/.test(mail);
    },

    validateUserName(username) {
      return username.length > 8
    },

    validatePassword(password) {
      return password.length > 8
    },

    async onSubmit(event) {
      event.preventDefault()

      // do some validation staff
      // if (!this.validateEmail(this.form.email)) {
      //   this.$nuxt.$emit('setMessage', {type: 'error', message: 'wrong email entered'})
      //   return
      // }
      //
      // if (!this.validateUserName(this.form.username)) {
      //   this.$nuxt.$emit('setMessage', {type: 'error', message: 'username must be longer then 8 characters'})
      //   return
      // }
      //
      // if (!this.validatePassword(this.form.password)) {
      //   this.$nuxt.$emit('setMessage', {type: 'error', message: 'password must be longer then 8 characters'})
      //   return
      // }

      //create new form and append fields
      let form = new FormData()
      form.append('email', this.form.email)
      form.append('firstName', this.form.firstName)
      form.append('lastName', this.form.lastName)
      form.append('age', parseInt(this.form.age))
      form.append('username', this.form.username)
      form.append('password', this.form.password)
      // form.append('avatar', this.avatarImage)

      //request to back end to save user
      this.$axios.post('/user/save', form).then(({data}) => {

        //emmit message to default layout
        this.$nuxt.$emit('setMessage', {type: data.status, message: data.status})

        // if everything is correct log in user
        if (data.status === 'success') {

          this.$auth.loginWith('local', {
            data: {
              username: this.form.username,
              password: this.form.password
            }
          })
        }
      })
    },

    onReset(event) {
      event.preventDefault()
      // Reset our form values
      this.form.email = null
      this.form.firstName = null
      this.form.lastName = null
      this.form.age = null
      this.form.username = null
      this.form.password = null
      // this.form.avatar = null
      // this.avatarImage = null
      // this.avatarImagePrev = null
    },
  },

  mounted() {
    $nuxt.$emit('setPageTitle', {title: 'Register'})
  }
}
</script>

<style scoped>

</style>
