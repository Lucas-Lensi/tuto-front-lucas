<template>
  <v-app dark>
    <v-navigation-drawer
      v-model="drawer"
      :mini-variant="miniVariant"
      :clipped="clipped"
      fixed
      app
    >
      
    </v-navigation-drawer>
    <v-app-bar
      :clipped-left="clipped"
      fixed
      app
    >
      Tuto Lucas
      <v-spacer></v-spacer>
      <nuxt-link to="/posts" class="mr-3"><v-btn>Posts</v-btn></nuxt-link>
      <div v-if="isUserLogged">
        <nuxt-link to="/connexion"><v-btn>Se connecter</v-btn></nuxt-link>
      </div>
      <div v-else>
        <nuxt-link to="/posts/create" class="mr-3"><v-btn>Creer post</v-btn></nuxt-link>
        <nuxt-link to="/users/my-profile" class="mr-3"><v-btn>Mon profile</v-btn></nuxt-link>
        <v-btn @click="logout()">Se déconnecter</v-btn>
      </div>
    </v-app-bar>
    <v-main>
      <v-container>
        <nuxt />
      </v-container>
    </v-main>
    <v-navigation-drawer
      v-model="rightDrawer"
      :right="right"
      temporary
      fixed
    >
      <v-list>
        <v-list-item @click.native="right = !right">
          <v-list-item-action>
            <v-icon light>
              mdi-repeat
            </v-icon>
          </v-list-item-action>
          <v-list-item-title>Switch drawer (click me)</v-list-item-title>
        </v-list-item>
      </v-list>
    </v-navigation-drawer>
   
  </v-app>
</template>

<script>
export default {
  data () {
    return {
      isUserLogged: null,
      clipped: false,
      drawer: false,
      fixed: false,
      items: [
        {
          icon: 'mdi-apps',
          title: 'Welcome',
          to: '/'
        },
        {
          icon: 'mdi-chart-bubble',
          title: 'Inspire',
          to: '/inspire'
        }
      ],
      miniVariant: false,
      right: true,
      rightDrawer: false,
      title: 'Vuetify.js'
    }
  },
  methods: {
    logout(){
      localStorage.clear();
      this.isUserLogged = false
      window.location.reload(true)
    }
  },
  async mounted(){
    this.isUserLogged = localStorage.token ? false : true
  }
}
</script>
