<template>
  <b-container>
    <div class="inner">
      <img src="http://mizani.jp/wp-content/uploads/store-1338629_1920.jpg" />
      <b-form-textarea id="textarea" v-model="text" rows="3" max-rows="6"></b-form-textarea>
      <b-btn color="info" @click="startSpeech">{{ recognitionText }}</b-btn>
    </div>
  </b-container>
</template>

<script>
  export default {
    data: function () {
      return {
        text: "",
        recognition: new webkitSpeechRecognition(),
        recognitionText: "音声入力開始"
      };
    },
    created: function () {
      this.recognition.onstart = () => {
        this.recognitionText = "音声入力中...";
      };
      this.recognition.onend = () => {
        this.recognitionText = "音声入力開始";
      };
      this.recognition.onresult = event => {
        if (event.results.length > 0) {
          this.text = event.results[0][0].transcript;
        }
      };
    },
    methods: {
      startSpeech: function () {
        this.recognition.lang = 'ja';
        this.recognition.start();
      }
    }
  }
</script>

<style>
  .inner {
    max-width: 400px;
    margin: 0 auto;
    text-align: center;
  }

  img {
    width: 100%;
  }
</style>