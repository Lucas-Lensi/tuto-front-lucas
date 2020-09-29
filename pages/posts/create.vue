<template>
  <div>
    <h1>create post</h1>
    <v-textarea v-model="content" color="teal">
      <template v-slot:label>
        <div>Content</div>
      </template>
    </v-textarea>
    <v-btn block light @click="createPost()">Submit</v-btn>
  </div>
</template>

<script>
import jwt_decode from 'jwt-decode'
import axios from 'axios'

export default {
  data() {
    return {
      content: '',
    }
  },
  methods: {
    createPost() {
      const post = {
        user: jwt_decode(localStorage.token).id,
        content: this.content,
      }
      axios
        .post('http://127.0.0.1:8000/post/create', post)
        .then((res) => {
          this.$router.push({ path: '/posts' })
        })
        .catch((err) => {
          console.log(err)
        })
    },
  },
  async beforeMount(){
    if (!localStorage.token) {
      this.$router.push({ path: '/connexion' })
    }
  }
}
</script>