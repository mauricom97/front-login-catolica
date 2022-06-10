<template>
  <div>
    <q-card class="login-card absolute-center" style="background-color: #5533ff">
      <q-card-section style="position: relative; left: 35%">
        <img src="../../public/img/password.png" style="max-width: 25%;">
      </q-card-section>
      <q-card-section>
        <q-input rounded standout v-model="username" style="margin-bottom: 15px;" label="Nome de usuario" />
        <q-input rounded standout v-model="password" style="margin-bottom: 15px;" label="Senha" />
      </q-card-section>
      <q-card-section>
        <q-toggle color="grey-1" v-model="rebemberCredentials" label="Lembrar minhas credenciais" />
      </q-card-section>
      <q-card-section>
        <q-btn label="Entrar" type="submit" v-on:click="authentication" color="primary" />
        <q-btn label="Limpar" type="reset" v-on:click="cleanInputsLogin" color="grey-1" flat class="q-ml-sm" />
      </q-card-section>
    </q-card>
  </div>
</template>

<style scoped>
.login-card {
  max-width: 35%;
  width: 35%;
  background-image: linear-gradient(to left bottom, #3f4174, #4b4988, #58519b, #6758af, #785fc2);
}
</style>

<script>
import axios from "axios"
export default {
  data() {
    return {
      username: null,
      password: null,
      rebemberCredentials: true
    }
  },
  methods: {
    async authentication() {

      let data = new Object()
      data.username = this.username
      data.password = this.password

      const config = {
        method: 'post',
        url: 'http://localhost:3352/users/autentication',
        headers: {
          'Content-Type': 'application/json'
        },
        data: data
      };

      await axios(config)
        .then(function (response) {
          console.log(JSON.stringify(response.data));
          window.localStorage.setItem('token', response.data.token);
        })
        .catch(function (error) {
          console.log(error);
        });
    },
    cleanInputsLogin() {
      this.username = ""
      this.password = ""
    }
  }
}
</script>
