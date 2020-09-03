<template>
  <div id="app" class="container">
    <Uploader 
      :options="{
        baseURL: 'http://saas.test/api'
      }"
    />
  </div>
</template>

<script>
import Uploader from '@/uploader/components/Uploader'

export default {
  name: 'App',
  components: {
    Uploader
  }
}
</script>

<style src="@/assets/styles/app.css"></style>


