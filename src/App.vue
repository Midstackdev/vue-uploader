<template>
  <div id="app" class="container">
    <Uploader 
      :options="{
        baseURL: 'http://lavel.test/api',
        maxConcurrentUploads: 1
      }"
      :handlers="{
       'video/mp4': {
          endpoint: 'uploads/video'
       }
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


