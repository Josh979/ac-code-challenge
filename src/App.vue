<template>
  <DonationForm />
</template>

<script>
import DonationForm from './components/DonationForm.vue'

export default {
  name: 'App',
  components: {
    DonationForm
  }
}
</script>

<style lang="scss">
  @import url('https://fonts.googleapis.com/css2?family=IBM+Plex+Sans:wght@300;400;500&family=Poppins:wght@600&display=swap');
  #app {
    font-family: 'IBM Plex Sans', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: left;
    font-size: 1rem;
    line-height: 1.25rem;
  }
</style>
