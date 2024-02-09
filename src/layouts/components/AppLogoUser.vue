<template>
  <v-img :src="logoPath" alt="App logo user"></v-img>
</template>

<script>
export default {
  name: 'AppLogoUser',
  data() {
    return {
      logoPath: null
    }
  },
  async created() {
    await this.loadLogo()
  },
  methods: {
    async loadLogo() {
      this.logoPath = (await import('@/assets/image/user_test.jpeg')).default
    }
  }
}
</script>
