<template>
  <v-app dark>
    <v-navigation-drawer
      v-model="drawer"
      :mini-variant="miniVariant"
      :clipped="clipped"
      fixed
      app
    >
      <v-list>
        <v-list-item
          v-for="(item, i) in items"
          :key="i"
          :to="item.to"
          router
          exact
        >
          <v-list-item-action>
            <v-icon>{{ item.icon }}</v-icon>
          </v-list-item-action>
          <v-list-item-content>
            <v-list-item-title v-text="item.title" />
          </v-list-item-content>
        </v-list-item>
      </v-list>
    </v-navigation-drawer>
    <v-app-bar
      :clipped-left="clipped"
      fixed
      app
    >
      <v-app-bar-nav-icon @click.stop="drawer = !drawer" />
    <!--  <v-btn
        icon
        @click.stop="miniVariant = !miniVariant"
      >
        <v-icon>mdi-{{ `chevron-${miniVariant ? 'right' : 'left'}` }}</v-icon>
      </v-btn>
      <v-btn
        icon
        @click.stop="clipped = !clipped"
      >
        <v-icon>mdi-application</v-icon>
      </v-btn>
      <v-btn
        icon
        @click.stop="fixed = !fixed"
      >
        <v-icon>mdi-minus</v-icon>
      </v-btn>  -->
      <v-toolbar-title v-text="title" />
      <v-spacer />
      <v-btn
        dark
        v-if="!isLoggedIn"
        class="ma-2" tile outlined color="success"
        to="/register"
       
      >
        Registrarse
      </v-btn>
      <v-btn dark v-else outlined   class="ma-2" tile @click="logout">Salir</v-btn>
    </v-app-bar>
    <v-content>
      <v-container>
        <nuxt />
      </v-container>
    </v-content>
    
    <v-footer
      :fixed="fixed"
      app
    >
      <span>&copy; {{ new Date().getFullYear() }}</span>
    </v-footer>
  </v-app>
</template>

<script>
import {  mapGetters } from 'vuex'
export default {
  data () {
    return {
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
          title: 'Reservar',
          to: '/reservar'
        },
        {
          icon: 'mdi-chart-bubble',
          title: 'Registrar Empresa',
          to: '/empresas'
        }
      ],
      miniVariant: false,
      right: true,
      rightDrawer: false,
      title: 'COVID HN'
    }
  },
      computed: {
    ...mapGetters({
      isLoggedIn: 'isLoggedIn'
    })
  },

  methods:{
      async logout() {
      try {
        await this.$fireAuth.signOut()
        this.$router.push('/login');
      } catch (e) {
        alert(e)
      }
    },
  }
}
</script>
