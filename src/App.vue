<template>
  <div id="login">
    <LoginForm v-if="!logged" @login="login" />
  </div>
</template>

<script>
import { ref } from 'vue'
import LoginForm from './components/LoginForm.vue'
import axios from 'axios'

export default {
  name: 'LayoutDefault',

  components: {
    LoginForm
  },

  data() {
    return {
      logged: false
    }
  },

  setup() {
    return {
      leftDrawerOpen: ref(false)
    }
  },

  methods: {
    login(e) {
      console.log(e)
      this.logged = true
    }
  },
  created() {
    const token = localStorage.getItem('token');
    if (token) {
      var config = {
        method: 'get',
        url: `http://localhost:3352/session/get/${token}`,
        headers: {}
      };

      axios(config)
        .then((response) => {
          console.log(JSON.stringify(response.data));
          if (response.data) {
            this.logged = true

            const params = {
              method: 'get',
              url: `http://localhost:3352/users/uuid/${response.data.uuidUser}`,
              headers: {}
            };

            axios(params)
              .then(function (response) {
                console.log(JSON.stringify(response.data));
              })
              .catch(function (error) {
                console.log(error);
              });

          }
        })
        .catch((error) => {
          console.log(error);
        });

    }

  }

}
</script>

<style>
body,
html {
  height: 100%;
  margin: 0;
  padding: 0;
  background-color: #5533ff;
}
</style>
