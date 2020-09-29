<template>
  <div>
    <center><h1>Mes informations :</h1></center>
    <v-row>
      <v-col cols="6">
        <v-card class="mx-auto mt-5 pb-5" outlined>
          <v-card-title> Informations personnelles : </v-card-title>
          <v-list-item three-line>
            pseudo : {{ user.pseudo }}<br />
            email : {{ user.email }}<br />
            bio : {{ user.bio }}<br />
            phone : {{ user.phone }}<br />
            addressLine : {{ user.addressLine }}<br />
            city : {{ user.city }}<br />
            zipcode : {{ user.zipcode }}<br />
            country : {{ user.country }}<br />
            inscriptionDate : {{ user.inscription_date }}<br />
          </v-list-item>
        </v-card>
      </v-col>
      <v-col cols="6">
        <v-card class="mx-auto mt-5" outlined>
          <v-card-title> Modifier mot de passe : </v-card-title>
          <v-list-item three-line>
            <v-col>
              <v-row>
                <v-text-field
                  outlined
                  v-model="oldPassword"
                  label="Old Password"
                  required
                ></v-text-field>
                <v-text-field
                  outlined
                  v-model="newPassword"
                  label="New Password"
                  required
                ></v-text-field>
              </v-row>
              <v-btn @click="editUserPassword()">Modifier password</v-btn>
            </v-col>
          </v-list-item>
        </v-card>
        <v-card class="mx-auto mt-5" outlined>
          <v-card-title>
            Modifier mes informations personnelles :
          </v-card-title>
          <v-list-item three-line>
            <v-col>
              <v-row>
                <v-text-field
                  outlined
                  v-model="user.pseudo"
                  label="Pseudo"
                  required
                ></v-text-field>
                <v-text-field
                  outlined
                  v-model="user.phone"
                  label="Phone"
                  required
                ></v-text-field>
              </v-row>
              <v-row>
                <v-text-field
                  outlined
                  v-model="user.address_line"
                  label="address_line"
                  required
                ></v-text-field>
                <v-text-field
                  outlined
                  v-model="user.city"
                  label="city"
                  required
                ></v-text-field>
              </v-row>
              <v-row>
                <v-text-field
                  outlined
                  v-model="user.zipcode"
                  label="ZipCode"
                  required
                ></v-text-field>
                <v-text-field
                  outlined
                  v-model="user.country"
                  label="Country"
                  required
                ></v-text-field>
              </v-row>
              <v-row>
                <v-text-field
                  outlined
                  v-model="user.email"
                  label="Email"
                  required
                ></v-text-field>
              </v-row>
              <v-btn @click="editUserInfos()">Modifier mes informations</v-btn>
            </v-col>
          </v-list-item>
        </v-card>
      </v-col>
    </v-row>
    <v-snackbar v-model="snackBar" :color="snackBarColor">
      {{ snackBarMsg }}

      <template v-slot:action="{ attrs }">
        <v-btn dark text v-bind="attrs" @click="snackBar = false">
          Close
        </v-btn>
      </template>
    </v-snackbar>
  </div>
</template>

<script>
import axios from 'axios'
import jwt_decode from 'jwt-decode'

export default {
  data() {
    return {
      user: {},
			userId: null,
			snackBarColor: '',
			snackBar: false,
			snackBarMsg: '',
			oldPassword : '',
			newPassword: ''
    }
  },
  methods: {
		async editUserPassword(){
			const userInfos = {
				email: this.user.email,
				password: this.oldPassword
			}
			 const isLogged = await axios.post('http://127.0.0.1:8000/login_check', userInfos)
			 .then((res) => { 
					const updateInfos = {
						email: this.user.email,
						password: this.newPassword
					}
					axios.put('http://127.0.0.1:8000/user/password', updateInfos)
					 .then((res)=>{
          	this.snackBarMsg = 'Password modifié'
          	this.snackBarColor = 'success'
          	this.snackBar = true
					 })
					 .catch((err)=>{
						console.log(err);
						this.snackBarMsg = 'Erreur'
          	this.snackBarColor = 'error'
          	this.snackBar = true
					 })
        })
        .catch((err) => {
          this.snackBarMsg = 'Identifiants incorrect'
          this.snackBarColor = 'error'
          this.snackBar = true
        })
		},
    async editUserInfos() {
			const userInfos = {
				'pseudo': this.user.pseudo,
				'email': this.user.email,
				'country': this.user.country,
				'zipcode': this.user.zipcode,
				'city': this.user.city,
				'addressLine': this.user.address_line,
				'phone': this.user.phone,
				'bio': this.user.bio
			}
			const updated = await axios.put('http://127.0.0.1:8000/user/update/' + this.userId, userInfos);
			if (updated.data === "ok") {
				this.snackBarMsg = 'Vos informations ont bien été mises a jour'
        this.snackBarColor = 'success'
        this.snackBar = true
			} else {
				this.snackBarMsg = 'Un problème est survenue'
        this.snackBarColor = 'error'
        this.snackBar = true
			}
		},
    getUserInfos() {
      if (localStorage.token === undefined) {
        this.$router.push({ path: '/connexion' })
      }
      const userId = jwt_decode(localStorage.token).id
      this.userId = userId
      axios
        .get('http://127.0.0.1:8000/user/' + userId)
        .then((res) => {
          this.user = res.data
        })
        .catch((err) => {
          console.log(err)
        })
    },
  },
  async mounted() {
    await this.getUserInfos()
	},
	async beforeMount(){
    if (!localStorage.token) {
      this.$router.push({ path: '/connexion' })
    }
  }
}
</script>