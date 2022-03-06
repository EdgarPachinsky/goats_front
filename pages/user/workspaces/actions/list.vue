<template>
  <div>
    <b-button
      type="button"
      variant="danger"
      class="margin-bottom-15"
      to="/user/workspaces/actions/create">Create new workspace</b-button>

<!--    <Workspaces/>-->
  </div>
</template>

<script>

export default {
  name: "create",

  middleware: 'auth',

  components: {

  },

  data() {
    return {
      wsImage: null,
      form: {
        name: null,
        description: null,
        image: null,
      },
    }
  },

  methods: {
    createImageUrl(event) {

      if (!event.target.files)
        return

      this.wsImage = event.target.files[0]
    },


    onReset(event) {
      event.preventDefault()
      // Reset our form values
      this.form.name = null
      this.form.description = null
      this.wsImage = null

    },

    async onSubmit(event) {
      event.preventDefault()


      //create new form and append fields
      let form = new FormData()
      this.form.name ? form.append('name', this.form.name) : ""
      this.form.description ? form.append('description', this.form.description) : ""
      this.wsImage ? form.append('image', this.wsImage) : ""

      //request to back end to save user
      this.$axios.post(`/workspaces`, form).then(({data}) => {

        //emmit message to default layout
        this.$nuxt.$emit('setMessage', {type: data.status, message: data.status})

        console.log(data)
        // if everything is correct log in user


        // this.$auth.loginWith('local', {
        //   data: {
        //     username: this.form.username,
        //     password: this.form.password
        //   }
        // })
        //
      })
    },

  },

  mounted() {
    $nuxt.$emit('setPageTitle', {title: 'Create workspace'})
  }

}
</script>

<style scoped>

</style>
