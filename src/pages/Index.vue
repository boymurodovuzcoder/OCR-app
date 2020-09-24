<template>
  <q-page class="flex flex-center">
  <div class="container container-fluid">
    <div class="row">
      <div class="form-group col">
        <label for="email">Enter URL:</label>
        <input type="text" v-model="url" class="form-control" id="email" aria-describedby="emailHelp" placeholder="enter url">
      </div>
      <div class="form-group col" style="margin-right: 0.5rem;">
        <label for="file">Upload Image or PDF File</label>
        <input type="file" class="form-control-file" id="file">
      </div>
    </div>
    <div v-show="loading" style="margin-right: 1rem;" class="spinner-border text-primary" role="status">
      <span class="sr-only">Loading...</span>
    </div>
    <transition name="slide-fade">
      <div v-if="inputValidation" class="alert alert-danger" role="alert">
        There is noting to convert!
      </div>
    </transition>
    <transition name="slide-fade">
      <div v-if="ErrorMessage" class="alert alert-danger" role="alert">
        {{ErrorMessage}}
      </div>
    </transition>
    <transition name="slide-fade">
      <div v-if="result" class="form-group" style="margin-top: 1rem;">
        <label for="exampleFormControlTextarea1">Extraxted Text: </label>
        <textarea class="form-control" id="exampleFormControlTextarea1" rows="5" v-model="result"></textarea>
      </div>
    </transition>
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
      fileBase64: null,
      ErrorMessage: false,
      inputValidation: false,
      loading: false,
      url: null,
      result: null
    }
  },
  methods: {
    onConvert () {
      var self = this
      var file = document.getElementById('file').files[0]
      self.loading = true
      self.result = null
      var form = new FormData()
      form.append('language', 'eng')
      form.append('scale', 'true')
      form.append('isOverlayRequired', 'false')
      form.append('isCreateSearchablePdf', 'false')
      form.append('isSearchablePdfHideTextLayer', 'false')
      form.append('detectOrientation', 'false')
      form.append('isTable', 'true')
      form.append('apikey', '6a3b74ad7588957')
      if (self.url) {
        console.log('from url')
        form.append('url', self.url)
        self.postRequest(form)
        self.ErrorMessage = false
        self.inputValidation = false
      } else if (file) {
        var reader = new FileReader()
        reader.readAsDataURL(file)
        reader.onload = function () {
          self.ErrorMessage = false
          self.inputValidation = false
          this.fileBase64 = reader.result
          form.append('base64Image', this.fileBase64)
          self.postRequest(form)
        }
        reader.onerror = function (error) {
          console.log('Error: ', error)
        }
      } else {
        self.loading = false
        self.inputValidation = true
      }
    },
    postRequest (data) {
      var self = this
      axios
        .post('https://api.ocr.space/parse/image', data)
        .then((res) => {
          self.loading = false
          if (res.data.ErrorMessage) {
            var errMsg = []
            res.data.ErrorMessage.forEach(ms => errMsg.push(ms))
            self.ErrorMessage = errMsg.join('')
          } else {
            self.result = res.data.ParsedResults[0].ParsedText
            console.log(res)
          }
        })
        .catch((err) => console.log(err))
    }
  }
}
</script>

<style scoped>
.container {
  background-color: #b6d3f9 !important;
  border-radius: 8px;
  padding: 1rem 1rem;
}
label {
  font-weight: 500;
}
.form-group {
  background-color: #c6d9f2;
  margin-left: 0.5rem;
  border-radius: 8px;
}
.slide-fade-enter-active {
  transition: all .3s ease;
}
.slide-fade-leave-active {
  transition: all .8s cubic-bezier(1.0, 0.5, 0.8, 1.0);
}
.slide-fade-enter, .slide-fade-leave-to
/* .slide-fade-leave-active до версии 2.1.8 */ {
  transform: translateX(10px);
  opacity: 0;
}
@media screen and (max-width: 426px) {
  .container {
    font-size: 0.7rem;
  }
}
</style>
