<template>
  <div>
    <b-button
      type="button"
      variant="danger"
      class="margin-bottom-15"
      to="/user/workspaces/actions/list">Back to list</b-button>

    <b-form
      @submit="onSubmit"
      @reset="onReset"
    >
      <b-row>
        <b-col sm="12" md="6" lg="6">
          <b-form-group id="input-group-1" label="Name:" label-for="name">
            <b-form-input
              id="name"
              v-model="form.name"
              type="text"
              placeholder="Enter ws name"
              required
            ></b-form-input>
          </b-form-group>

          <b-form-group id="input-group-2" label="Description:" label-for="description">
            <b-form-input
              id="description"
              v-model="form.description"
              type="text"
              placeholder="Enter ws description"
              required
            ></b-form-input>
          </b-form-group>

          <b-form-group
            description="Image"
            id="input-group-3" label="Image:" label-for="image">
            <b-form-file
              id="image"
              @change="createImageUrl($event)"
              v-model="form.image"
              class="margin-bottom-6"
              placeholder="Choose a file or drop it here..."
              drop-placeholder="Drop file here..."
            ></b-form-file>
          </b-form-group>
          <hr>
          <b-button type="submit" variant="">Submit</b-button>
          <b-button type="reset" variant="danger" @click="onReset">Reset</b-button>
        </b-col>
      </b-row>
    </b-form>
  </div>
</template>

<script>

export default {
  name: "create",

  middleware: 'auth',

  components: {},

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
