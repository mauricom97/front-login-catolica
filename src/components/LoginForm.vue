<template>
  <div>
    <q-card class="login-card absolute-center" style="background-color: #5533ff">
      <q-card-section style="position: relative; left: 35%">
        <img src="../../public/img/password.png" style="max-width: 25%;">
      </q-card-section>
      <q-card-section>
        <q-input rounded outlined v-model="username" style="margin-bottom: 15px;" label="Nome de usuario" />
        <q-input rounded outlined v-model="password" type="password" style="margin-bottom: 15px;" label="Senha" />
      </q-card-section>
      <q-card-section>
        <q-toggle color="purple-6" v-model="rebemberCredentials" label="Lembrar minhas credenciais" />
      </q-card-section>
      <q-card-section>
        <q-btn label="Entrar" type="submit" v-on:click="authentication" color="primary" />
        <q-btn label="Limpar" type="reset" v-on:click="cleanInputsLogin" color="grey-1" flat class="q-ml-sm" />
        <br />
        <q-btn class="q-mt-xs" @click="signup = true" label="Criar usuario" type="submit" v-on:click="createUser"
          color="green-6" />
      </q-card-section>
    </q-card>

    <q-dialog v-model="signup">
      <q-card style="width: 100%;">
        <q-card-section>
          <div class="text-h6">Sign up</div>
        </q-card-section>

        <q-card-section class="q-pt-none">
          <q-input rounded outlined v-model="signForm.fullname" label="Nome completo" />
          <q-input rounded type="email" class="q-my-sm" outlined v-model="signForm.email" label="E-mail" />
          <q-input rounded class="q-my-sm" outlined v-model="signForm.username" label="Nome de usuario" />
          <q-input rounded type="password" class="q-my-sm" outlined v-model="signForm.password1"
            v-on:keyup="validPassword()" label="Nova senha" />
          <q-linear-progress :value="progress" :buffer="buffer" :color="colorLevelPassword" class="q-mt-sm" />
          <q-input rounded type="password" class="q-my-sm" outlined v-model="signForm.password2"
            label="Repita a senha" />
          <small style="color: red" v-if="incorretPassword">As senhas precisam ser iguais</small>
          <q-card-section>
            <q-btn label="Cadastrar" type="submit" v-on:click="signUp" color="primary" />
            <q-btn label="Limpar" type="reset" v-on:click="cleanInputs(signForm)" color="grey-10" flat
              class="q-ml-sm" />
          </q-card-section>
        </q-card-section>
      </q-card>
    </q-dialog>


    <q-dialog v-model="dialog" :position="position">
      <q-card style="width: 350px">
        Usuario criado com sucesso
      </q-card>
    </q-dialog>


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
import { ref } from 'vue'
export default {
  setup() {
    const dialog = ref(false)
    const position = ref('left')
    return {
      progress: ref(0.00),
      buffer: ref(0.00),
      icon: ref(false),

      dialog,
      position,

      open(pos) {
        position.value = pos
        dialog.value = true
      }
    }
  },
  props: ['user'],
  data() {
    return {
      signup: ref(false),
      username: null,
      password: null,
      rebemberCredentials: true,
      signForm: new Object(),
      incorretPassword: false,
      colorLevelPassword: 'red-14'
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
        .then((response) => {
          console.log(JSON.stringify(response.data));
          if (response.data.token) {
            let sessionInfo = new Object()
            sessionInfo.token = response.data.token
            sessionInfo.uuidUser = response.data.uuid
            window.localStorage.setItem('token', response.data.token);
            this.createSession(sessionInfo)
            this.$emit('login', response.data)

          } else {
            console.log("error")
          }
        })
        .catch((error) => {
          console.log(error);
        });
    },
    cleanInputs(Object) {
      for (let property in Object) {
        Object[property] = ''
      }
    },
    async signUp() {
      if (this.signForm.password1 != this.signForm.password2) {
        this.incorretPassword = true
        setTimeout(() => {
          this.incorretPassword = false
        }, 3000)
      } else {
        const data = JSON.stringify({
          "name": this.signForm.fullname,
          "username": this.signForm.username,
          "password": this.signForm.password1,
          "email": this.signForm.email
        });

        const config = {
          method: 'post',
          url: 'http://localhost:3352/users/create',
          headers: {
            'Content-Type': 'application/json'
          },
          data: data
        };

        await axios(config)
          .then((response) => {
            console.log(JSON.stringify(response.data));
            this.cleanInputs(this.signForm)
            this.progress = 0.00
            this.buffer = 0.00
            this.open('top')
          })
          .catch((error) => {
            console.log(error);
          });

      }
    },
    createSession(sessionInfo) {
      let data = new Object()
      data.uuidUser = sessionInfo.uuidUser.uuid
      data.token = sessionInfo.token

      var config = {
        method: 'post',
        url: 'http://localhost:3352/session/create',
        headers: {
          'Content-Type': 'application/json'
        },
        data: data
      };

      axios(config)
        .then(function (response) {
          console.log(JSON.stringify(response.data));
        })
        .catch(function (error) {
          console.log(error);
        });

    },
    validPassword() {
      if (this.signForm.password1.length >= 3 && this.signForm.password1.length < 5) {
        this.progress = 0.05
        this.buffer = 0.05
      }
      if (this.signForm.password1.length >= 5 && this.signForm.password1.length < 10) {
        this.progress = 0.20
        this.buffer = 0.20
        this.colorLevelPassword = "deep-orange-6"
      }
      if (this.signForm.password1.length >= 10 && this.signForm.password1.length < 15) {
        this.progress = 0.50
        this.buffer = 0.50
        this.colorLevelPassword = "yellow-12"
      }
      if (this.signForm.password1.length >= 15) {
        this.progress = 1.00
        this.buffer = 1.00
        this.colorLevelPassword = "light-green-14"
      }
      if (this.signForm.password1.length < 3) {
        this.progress = 0.00
        this.buffer = 0.00
        this.colorLevelPassword = "red-14"
      }

    }
  }
}
</script>
