<template>
  <div>

    <h1>Hello in our workspace</h1>
    <hr>
    <h2>What can you do here</h2>
    <ul>
      <li>
        action 1
      </li>
      <li>
        action 2
      </li>
      <li>
        action 3
      </li>
      <li>
        action 4
      </li>
      <li>
        action 5
      </li>
    </ul>
    <hr>
    <b-button
      type="button"
      variant="danger"
      class="margin-bottom-15"
      to="/user/cabinet">Lets get started</b-button>
  </div>
</template>

<script>
// import WorkspaceList from "../components/Workspaces";

export default {

  components: {
    // WorkspaceList,
  },

  data() {
    return{

    }
  },

  methods: {

  },

  mounted() {
    $nuxt.$emit('setPageTitle', {title: 'Multi tool for tracking device information'})
  }
}
</script>
