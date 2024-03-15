<template>
  <Banner />
  <div class="main-container">
    <h1>Monte o seu Burguer:</h1>
    <BurguerForm />
  </div>
</template>

<script>
// @ is an alias to /src
import Banner from '../components/Banner.vue'
import BurguerForm from '@/components/BurguerForm.vue'

export default {
  name: 'Home',
  components: {
    Banner,
    BurguerForm
  }
}
</script>
