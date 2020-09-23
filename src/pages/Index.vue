<template>
  <q-page class="flex flex-center">
  <div class="container container-fluid">
    <div class="row">
      <div class="form-group col">
        <label for="email">Enter URL:</label>
        <input type="text" class="form-control" id="email" aria-describedby="emailHelp" placeholder="enter url">
      </div>
      <div class="form-group col">
        <label for="file">Upload Image or PDF File</label>
        <input type="file" class="form-control-file" id="file">
      </div>
    </div>
    <div class="alert alert-danger" role="alert">
      File size should be less than 1 MB
    </div>
    <button type="button" class="btn btn-outline-secondary" id="submit" @click="onConvert">convert</button>
  </div>
  </q-page>
</template>

<script>
import axios from 'axios'

export default {
  name: 'PageIndex',
  data () {
    return {
      fileBase64: null
    }
  },
  methods: {
    onConvert () {
      var file = document.getElementById('file').files[0]
      var reader = new FileReader()
      reader.readAsDataURL(file)
      reader.onload = function () {
        this.fileBase64 = reader.result
        console.log(this.fileBase64)
      }
      reader.onerror = function (error) {
        console.log('Error: ', error)
      }
      console.log(this.fileBase64)
      var form = new FormData()
      form.append('base64Image', this.fileBase64)
      form.append('language', 'eng')
      form.append('scale', 'true')
      form.append('isOverlayRequired', 'false')
      form.append('isCreateSearchablePdf', 'false')
      form.append('isSearchablePdfHideTextLayer', 'false')
      form.append('detectOrientation', 'false')
      form.append('isTable', 'true')
      form.append('apikey', '6a3b74ad7588957')

      axios
        .post('https://api.ocr.space/parse/image', form)
        .then((res) => console.log(res))
        .catch((err) => console.log(err))
    }
  }
}
</script>

<style scoped>
</style>
