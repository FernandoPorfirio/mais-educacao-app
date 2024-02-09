<script>
import { LOGIN } from '@/constants/router.js'
import AppLogoUser from '@/layouts/components/AppLogoUser.vue'
AppLogoUser

export default {
  data: () => ({
    logoutOption: { text: 'Logout', icon: 'mdi-logout' }
  }),
  computed: {
    userName() {
      return this.$store.state.auth.user && this.$store.state.auth.user.user.name
        ? this.$store.state.auth.user.user.name
        : ''
    },
    userEmail() {
      return this.$store.state.auth.user && this.$store.state.auth.user.user.email
        ? this.$store.state.auth.user.user.email
        : ''
    }
  },
  components: {
    AppLogoUser
  },
  methods: {
    async logout() {
      this.$store.dispatch('auth/logout')
      this.$router.push(LOGIN.path)
    }
  }
}
</script>

<template>
  <v-menu>
    <template #activator="{ props }">
      <v-avatar v-bind="props">
        <app-logo-user />
      </v-avatar>
    </template>
    <v-card min-width="200px">
      <v-list :lines="false" density="compact" nav>
        <v-list-item :title="userName" :subtitle="userEmail"></v-list-item>
          <v-list-item :prepend-icon="logoutOption.icon" @click="logout">
            <v-list-item-title>{{ logoutOption.text }}</v-list-item-title>
          </v-list-item>
      </v-list>
    </v-card>
  </v-menu>
</template>
