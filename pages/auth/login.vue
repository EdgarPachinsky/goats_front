<template>
  <b-form
    @submit="onSubmit"
    @reset="onReset"
    >
    <b-row>
      <b-col sm="12" md="6" lg="6">
        <b-form-group id="input-group-1" label="Username:" label-for="username">
          <b-form-input
            id="username"
            v-model="form.username"
            type="text"
            placeholder="Enter your username"
            required
          ></b-form-input>
        </b-form-group>
        <b-form-group
          id="input-group-2" label="Password:" label-for="password">
          <b-form-input
            id="password"
            v-model="form.password"
            placeholder="Enter password"
            type="password"
            required
          ></b-form-input>
        </b-form-group>

      </b-col>


<!--      <b-col sm="12" md="6" lg="6">-->
<!--        <h2>Or use</h2>-->

<!--        <b-button type="button" variant="" @click="loginWithFB">-->
<!--          <b-icon icon="facebook"></b-icon>-->
<!--        </b-button>-->

<!--        <b-button type="button" variant="">-->
<!--          <b-icon icon="google"></b-icon>-->
<!--        </b-button>-->

<!--        <b-button type="button" variant="">-->
<!--          <b-icon icon="github"></b-icon>-->
<!--        </b-button>-->
<!--      </b-col>-->

      <b-col sm="12">
        <hr>
        <b-button type="submit" variant="">Login</b-button>
        <b-button type="reset" variant="danger" @click="onReset">Reset</b-button>
<!--        <b-button type="button" variant="danger" to="/auth/reset-password/email">Forget Password</b-button>-->
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
      form: {
        username: null,
        password: null
      }
    }
  },

  methods: {
    createImageUrl(event) {

      if (!event.target.files)
        return

      this.avatarImage = event.target.files[0]
      this.avatarImagePrev = URL.createObjectURL(this.avatarImage)
    },

    onSubmit(event){
      event.preventDefault()

      this.$auth.loginWith("local" , {
        data : {
          username: this.form.username,
          password: this.form.password
        }
      }).then(({data}) => {
        console.log(data)
        this.$nuxt.$emit('setMessage', {type: data.status, message: data.message })
      })
    },

    onReset(event) {
      event.preventDefault()

      this.form.username = ''
      this.form.password = ''
    },

    loginWithFB(){
      this.$auth.loginWith('facebook').then(({data}) => {
        this.$router.push('/')
      })
    }
  },

  mounted() {
    $nuxt.$emit('setPageTitle', {title: 'Login'})
  }
}
</script>

<style scoped>

</style>
