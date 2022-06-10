<template>
  <div id="login">
    <login />
  </div>
</template>

<script>
import { ref } from 'vue'
import login from './components/login.vue'

export default {
  name: 'LayoutDefault',

  components: {
    login
  },

  setup() {
    return {
      leftDrawerOpen: ref(false)
    }
  }
}
</script>

<style>
body, html {
  height: 100%;
  margin: 0;
  padding: 0;
  background-color: #5533ff;
}
</style>
