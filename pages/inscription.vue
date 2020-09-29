<template>
  <v-form>
    <v-container>
      <v-row>
        <v-col
          cols="12"
          md="4"
        >
          <v-text-field
            outlined
            v-model="user.pseudo"
            label="Pseudo"
            required
          ></v-text-field>
        </v-col>

        <v-col
          cols="12"
          md="4"
        >
          <v-text-field
           outlined
            v-model="user.bio"
            label="Bio"
            required
          ></v-text-field>
        </v-col>

        <v-col
          cols="12"
          md="4"
        >
          <v-text-field
           outlined
            v-model="user.phone"
            label="Phone"
            required
          ></v-text-field>
        </v-col>
      </v-row>
      <v-row>
        <v-col
          cols="12"
          md="3"
        >
          <v-text-field
            outlined
            v-model="user.addressLine"
            label="Address Line"
            required
          ></v-text-field>
        </v-col>

        <v-col
          cols="12"
          md="3"
        >
          <v-text-field
           outlined
            v-model="user.city"
            label="City"
            required
          ></v-text-field>
        </v-col>

        <v-col
          cols="12"
          md="3"
        >
          <v-text-field
           outlined
            v-model="user.zipcode"
            label="ZipCode"
            required
          ></v-text-field>
        </v-col>
         <v-col
          cols="12"
          md="3"
        >
          <v-text-field
           outlined
            v-model="user.country"
            label="Country"
            required
          ></v-text-field>
        </v-col>
      </v-row>
      <v-row>
          <v-col
            cols="12"
            md="6"
          >
            <v-text-field
            outlined
            v-model="user.email"
            label="Email"
            required
            ></v-text-field>
          </v-col>
          <v-col
            cols="12"
            md="6"
          >
           <v-text-field
            outlined
            v-model="user.password"
            label="Password"
            required
            ></v-text-field>

          </v-col>
      </v-row>
      <v-row>
            <v-btn block dark @click="inscription()">Submit</v-btn>
      </v-row>
      <nuxt-link to="/connexion">Déjà un ? login</nuxt-link>
    </v-container>
  </v-form>
</template>


<script>
import axios from 'axios'

export default {
    data(){
        return {
            user: {
                pseudo: '',
                bio: '',
                phone: '',
                addressLine: '',
                city: '',
                zipcode: '',
                country: '',
                email: '',
                password: ''
            }
        }
    },
    methods: {
      async login() {
        const isLogged = await axios
          .post('http://127.0.0.1:8000/login_check', this.user)
          .then((res) => {
            localStorage.setItem('token', res.data.token)
            this.user = {}
            this.$router.push({ path: '/posts' })
          })
          .catch((err) => {
          })
        },
        async postUser(user){
            const res = await this.$axios.post('http://127.0.0.1:8000/user/create', user);
            return res.data;
        },
        async inscription(){
            await this.postUser(this.user);
            await this.login();
        }
    },
    async beforeMount(){
      if (localStorage.token) {
        this.$router.push({ path: '/posts' })
      }
    }
}
</script>