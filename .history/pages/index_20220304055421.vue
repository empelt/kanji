<template>
  <b-container>
    <div class="inner">
      <img src="http://mizani.jp/wp-content/uploads/store-1338629_1920.jpg" />
      <b-btn color="info" @click="startSpeech" style="margin:10px">
        {{
        recognitionText
        }}
      </b-btn>
      <div id="overlay" v-show="showContent" v-on:click="closeModal">
        <div id="modal">
          <p>Select the Kanji</p>
          <div class="b-container modal-word-container">
            <div v-for="kanji in kanjis" v-bind:key="kanji.id" v-on:click="selectKanji(kanji)">
              <div class="b-card modal-word-card">
                <div class="modal-card-content">
                  <p>{{ kanji["kanji"] }}</p>
                </div>
              </div>
              <p class="kanji-mean">{{ kanji['means'].length ? kanji['means'][0]['mean'] : '' }}</p>
            </div>
          </div>
          <p>
            <b-btn color="info" v-on:click="closeModal">close</b-btn>
          </p>
        </div>
      </div>
      <div class="b-container word-container">
        <div
          class="b-card word-card"
          v-for="(word, index) in final_textarray"
          v-bind:key="word.id"
          v-on:click="openModal(index)"
          v-bind:class="{ small: is_smallarray[index] }"
        >
          <div class="card-content">
            <p>{{ word["kanji"] ? word["kanji"] : word }}</p>
          </div>
        </div>
      </div>
      <p v-show="!textarray.length == 0">Please tap each characters and select KANJI</p>
      <img src="http://mizani.jp/wp-content/uploads/store-1338629_1920.jpg" />
      <img src="http://mizani.jp/wp-content/uploads/store-1338629_1920.jpg" />
      <img src="http://mizani.jp/wp-content/uploads/store-1338629_1920.jpg" />
    </div>
  </b-container>
</template>

<script>
import kanjis from "@/assets/json/kanji.json";
import smalls from "@/assets/json/small.json";
import axios from "axios";

function hiraToKana(str) {
  return str.replace(/[\u3041-\u3096]/g, function(match) {
    var chr = match.charCodeAt(0) + 0x60;
    return String.fromCharCode(chr);
  });
}

export default {
  data: function() {
    return {
      kanjis: null,
      text: "",
      recognition: new webkitSpeechRecognition(),
      recognitionText: "音声入力開始",
      textarray: [],
      final_textarray: [],
      is_smallarray: [],
      showContent: false,
      card_id: 0
    };
  },
  created: function() {
    this.recognition.onstart = () => {
      this.recognitionText = "音声入力中...";
    };
    this.recognition.onend = () => {
      this.recognitionText = "音声入力開始";
    };
    this.recognition.onresult = event => {
      if (event.results.length > 0) {
        this.textarray = [];
        this.final_textarray = [];
        this.is_smallarray = [];
        this.text = event.results[0][0].transcript;
        axios
          .post("https://labs.goo.ne.jp/api/hiragana", {
            app_id:
              "547f4b5519594402d36cf66174ba354e18404fd4c1a6c05882ec745436916a1b",
            request_id: "record003",
            sentence: event.results[0][0].transcript,
            output_type: "hiragana"
          })
          .then(response => {
            for (const word of response.data["converted"].replace(/\s+/g, "")) {
              if (word in smalls) {
                this.is_smallarray.push(true);
                this.textarray.push(smalls[word]);
                this.final_textarray.push(smalls[word]);
              } else {
                this.is_smallarray.push(false);
                this.textarray.push(word);
                this.final_textarray.push(word);
              }
            }
            this.text = response.data["converted"];
          });
        // for (const word of this.text) {
        //   this.textarray.push(word);
        //   this.final_textarray.push(word);
        // }
      }
    };
  },
  methods: {
    startSpeech: function() {
      this.recognition.lang = "ja";
      this.recognition.start();
    },
    openModal: function(e) {
      this.showContent = true;
      this.card_id = e;
      axios
        .post("http://localhost:4200/kanjis/findAllByKana", {
          kana: this.final_textarray[e]["katakana"]
            ? this.final_textarray[e]["katakana"]
            : hiraToKana(this.final_textarray[e])
        })
        .then(kanjires => {
          console.log(kanjires);
          this.kanjis = kanjires["data"];
        });
    },
    closeModal: function() {
      this.showContent = false;
      this.kanjis = null;
    },
    selectKanji: function(e) {
      this.final_textarray[this.card_id] = e;
    }
  }
};
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

.word-container {
  display: flex;
  justify-content: center;
  flex-wrap: wrap;
}
.word-card {
  width: 64px;
  height: 64px;
  margin: 0.5rem;
  border: solid 1px black;
  position: relative;
}

.small {
  width: 32px;
  height: 32px;
  margin: 0.5rem;
  border: solid 1px black;
  position: relative;
}

.small p {
  font-size: 22px!important;
}
.card-content {
  position: absolute;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  margin: auto;
  width: 80%;
  height: 3.2rem;
}
.card-content p {
  font-size: 34px;
}

.modal-word-container {
  display: flex;
  justify-content: center;
  flex-wrap: wrap;
}
.modal-word-card {
  width: 64px;
  height: 64px;
  margin: 0.5rem;
  border: solid 1px black;
  position: relative;
}
.modal-card-content {
  position: absolute;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  margin: auto;
  width: 80%;
  height: 3.2rem;
}
.modal-card-content p {
  font-size: 34px;
}

#overlay {
  /*　要素を重ねた時の順番　*/
  z-index: 1;

  /*　画面全体を覆う設定　*/
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);

  /*　画面の中央に要素を表示させる設定　*/
  display: flex;
  align-items: center;
  justify-content: center;
}
#modal {
  z-index: 2;
  width: 340px;
  /* width: 80%; */
  padding: 1em;
  background: #fff;
}

.kanji-mean {
  max-width: 90px;
  overflow-wrap: anywhere;
  margin: 0 auto;
}
</style>
