<template>
  <div>
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
  </div>
</template>

<script>
import axios from 'axios'

export default {
  data() {
    return {
        user: {}
    }
  },
  methods: {
    getUserInfos() {
      axios
        .get('http://127.0.0.1:8000/user/' + this.$route.params.id)
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
}
</script>