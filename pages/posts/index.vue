<template>
  <div>
    <center><h1 class="mb-5">Tous les posts</h1></center>
    <div class="my-5" v-for="(post, index) in posts" v-bind:key="index">
      <v-card class="mx-auto mt-5" max-width="748" outlined>
        <v-list-item three-line>
          <v-list-item-content>
            <v-list-item-title class="headline mb-1">
               <nuxt-link :to="'/users/' + post.user.id">{{post.user.pseudo}}</nuxt-link>
            </v-list-item-title>
            <v-list-item-subtitle>{{ post.content }}</v-list-item-subtitle>
          </v-list-item-content>
        </v-list-item>
        <v-card-actions>
          <v-btn icon @click="likePost(post)">
            <v-icon v-if="isLikedByUser[post.id]" color="red">mdi-heart</v-icon>
            <v-icon v-else>mdi-heart</v-icon>
          </v-btn>
          {{ post.likes.length }}
          <v-spacer></v-spacer>
          {{ post.comments.length }}
          <v-btn text @click="showCommentsSection(post.id)">Comment</v-btn>
        </v-card-actions>
      </v-card>
      <div
        :id="'comment-section-' + post.id"
        class="comment-section px-4 py-5 no-display"
      >
        <div v-for="(comment, index) in post.comments">
          <div class="comment">
            <nuxt-link :to="'/users/' + post.user.id">{{comment.user.pseudo}}</nuxt-link> :<br />
            {{ comment.content }}
          </div>
        </div>
        <v-text-field
          label="Votre commentaire"
          outlined
          dense
          class="mt-5 pt-5"
          v-model="comment"
        >
        </v-text-field>
        <v-btn @click="commentPost(post)">Commenter</v-btn>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
import jwt_decode from 'jwt-decode'

export default {
  data() {
    return {
      posts: [],
      userId: null,
      dialog: false,
      isLikedByUser: [],
      comment: '',
    }
  },
  methods: {
    async getPostss() {
      const res = await axios.get('http://127.0.0.1:8000/post/all')
      this.posts = res.data
      await this.posts.forEach((post) => {
        axios
          .get('http://127.0.0.1:8000/likes/' + post.id + '/' + this.userId)
          .then((res) => {
            if (res.data == null) {
              this.$set(this.isLikedByUser, post.id, false)
            } else {
              this.$set(this.isLikedByUser, post.id, true)
            }
          })
      })
    },
    async unlikePost(post) {
      const res = await axios.get(
        'http://127.0.0.1:8000/likes/' + post.id + '/' + this.userId
      )
      const likeId = res.data
      const unlike = await axios.delete(
        'http://127.0.0.1:8000/likes/delete/' + likeId
      )
      this.$set(this.isLikedByUser, post.id, false)
      post.likes.length -= 1
    },
    async likePost(post) {
      if (this.userId === null) {
        this.$router.push({ path: 'connexion' })
      }
      if (!this.isLikedByUser[post.id]) {
        const data = {
          user: this.userId,
          posts: post.id,
        }
        await axios.post('http://127.0.0.1:8000/likes/create', data)
        this.$set(this.isLikedByUser, post.id, true)
        post.likes.length += 1
      } else {
        this.unlikePost(post)
      }
    },
    async commentPost(post) {
		const data = {
			posts: post.id,
			content: this.comment,
			user: this.userId
		}
		const res = await axios.post('http://127.0.0.1:8000/comment/create', data);
		this.comment = ''
		var newComment = {
			user: {
				pseudo: res.data.user
			},
			content: res.data.content
		}
		this.$set(post.comments, post.comments.length, newComment)
	},
    showCommentsSection(blogId) {
      document
        .getElementById('comment-section-' + blogId)
        .classList.toggle('no-display')
    },
    async getUser() {
      if (localStorage.token === undefined) {
        return
      }
      this.userId = jwt_decode(localStorage.token).id
    },
  },
  async created() {
    this.getUser()
    this.getPostss()
  },
}
</script>

<style src="@/assets/css/posts.css"></style>