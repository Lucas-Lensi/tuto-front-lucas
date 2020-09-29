<template>
  <v-form>
    <v-container>
      <v-row>
        <v-col cols="12" md="6">
          <v-text-field
            outlined
            v-model="user.email"
            label="Email"
            required
          ></v-text-field>
        </v-col>
        <v-col cols="12" md="6">
          <v-text-field
            outlined
            v-model="user.password"
            label="Password"
            required
          ></v-text-field>
        </v-col>
      </v-row>
      <v-row>
        <v-btn block light @click="login()">Submit</v-btn>
      </v-row>
      <nuxt-link to="/inscription">Pas encore de compte ? signup</nuxt-link>
      <v-snackbar v-model="snackBar" :color="snackBarColor">
        {{ snackBarMsg }}

        <template v-slot:action="{ attrs }">
          <v-btn dark text v-bind="attrs" @click="snackBar = false">
            Close
          </v-btn>
        </template>
      </v-snackbar>
    </v-container>
  </v-form>
</template>


<script>
import axios from 'axios'
export default {
  data() {
    return {
      user: {
        email: '',
        password: '',
      },
      snackBar: false,
      snackBarMsg: '',
      snackBarColor: '',
    }
  },
  methods: {
    async login() {
      const isLogged = await axios
        .post('http://127.0.0.1:8000/login_check', this.user)
        .then((res) => {
          localStorage.setItem('token', res.data.token)
          this.user = {}
          window.location.reload(true)
       })
        .catch((err) => {
          this.snackBarMsg = 'Identifiants incorrect'
          this.snackBarColor = 'error'
          this.snackBar = true
        })
    },
  },
  async beforeMount(){
    if (localStorage.token) {
      this.$router.push({ path: '/posts' })
    }
  }
}
</script>