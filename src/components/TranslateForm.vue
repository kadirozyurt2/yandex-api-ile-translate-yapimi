<template>
  <form @submit.prevent="translateIt" class="well">
    <textarea v-model="translateText" cols="30" rows="5" class="form-control" placeholder="Çevirmek istediğiniz kelime/cümle yazınız."></textarea>
    <select v-model="translateTo " class="form-control">
      <option v-for="(value, key) in languages" :value="key">{{ value }}</option>
    </select>
    <br>
    <div class="text-left" v-if="detectedLang">
      <strong>Tespit Edilen Dil : </strong> {{ detectedLang }}
    </div>
    <br>
    <button type="submit" class="btn btn-primary btn-block">Çevir Gelsin!</button>
  </form>
</template>
<script>
  import axios from "axios"
  export default {
    data(){
      return {
        translateText : '',
        translateTo : '',
        languages : {},
        detectedLang : '',
      }
    },
    methods : {
      translateIt(){
        let url = "https://translate.yandex.net/api/v1.5/tr.json/translate?key=trnsl.1.1.20181002T212402Z.845255b7ffa94333.9ec78bf8282c3d1446a9e98dfd3ae4158618eab4&text=" + this.translateText + "&lang=" + this.translateTo
        axios.get(url)
          .then(response => {
            console.log(response);

            this.detectedLang = this.languages[this.translateTo]
            this.$emit("translatedEvent", response.data.text[0])

            let history = {
              from : this.languages[response.data.lang.split("-")[0]],
              to : this.detectedLang,
              translateText : this.translateText,
              translatedText : response.data.text[0],
            }
            this.$emit("historyEvent", history)

            this.translateTo = ''
            this.translateText = ''

          }).catch(e => console.log(e))
      }
    },
    created(){
      axios.get("https://translate.yandex.net/api/v1.5/tr.json/getLangs?key=trnsl.1.1.20181002T212402Z.845255b7ffa94333.9ec78bf8282c3d1446a9e98dfd3ae4158618eab4&ui=tr")
        .then(response => {
          this.languages = response.data.langs
        })
        .catch(e => console.log(e))
    }
  }
</script>
<style></style>
