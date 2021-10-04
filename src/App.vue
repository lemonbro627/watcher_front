<template>
  <div id="app">
    <div class="wrapper">
      <div class="wrapper_video">
        <div class="wrapper_video__video">
          <video ref="video" autoplay preload @ended="endedPlaying"/>
        </div>
        <div class="spacer"/>
        <div class="wrapper_video__rss" v-html="htmlRSS"/>
      </div>
      <div class="wrapper_content">
        <div class="wrapper_content__photo">
          <div class="wrapper_content__photo_text"><h2>{{ this.textBeforePhoto }}</h2></div>
          <div class="wrapper_content__photo_photo"><img class="photo_content" ref="photo"/></div>
          <div class="wrapper_content__photo_text"><h3>{{ this.textAfterPhoto }}</h3></div>
        </div>
        <div class="wrapper_content__time">
          <h1 class="block_time">{{ ClockTime }}</h1>
          <img class="medved" src="../public/medved.png"/>
          <h2 class="block_date">{{ ClockDate }}</h2>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      urlVideo: null,
      urlPhoto: null,
      textBeforePhoto: null,
      textAfterPhoto: null,
      htmlRSS: null,
      rss_timer: '',
      photo_timer: '',
      ClockDate: null,
      ClockTime: null,
      time_timer: ''
    }
  },
  methods: {
    getVideo() {
      let axios = require('axios');
      axios.get('http://127.0.0.1:8000/getNewVideo')
          .then((response) => {
            // handle success
            console.log(response.data);
            this.urlVideo = response.data.url
            this.$refs.video.src = response.data.url
          });
      // console.log(this.urlVideo)
    },
    getPhoto() {
      let axios = require('axios');
      axios.get('http://127.0.0.1:8000/getNewPhoto')
          .then((response) => {
            // handle success
            console.log(response.data);
            this.$refs.photo.src = response.data.url
            this.textAfterPhoto = response.data.text_after
            this.textBeforePhoto = response.data.text_before
          });
    },
    getRSS() {
      let axios = require('axios');
      axios.get('http://127.0.0.1:8000/getNewFeed')
          .then((response) => {
            // handle success
            console.log(response.data);
            this.htmlRSS = response.data
          });
    },
    getNow: function() {
      [this.ClockDate, this.ClockTime] = ((l,d) => [d.toLocaleDateString(l).replace(/\./g, '-'), d.toLocaleTimeString(l)])('ru-RU', new Date());    },
    endedPlaying(){
      // console.log("ended");
      this.getVideo();
    }
  },
  mounted() {
    this.getVideo()
    this.getPhoto()
    this.getRSS()
    this.time_timer = setInterval(this.getNow, 1000)
    this.rss_timer = setInterval(this.getRSS, 40000)
    this.photo_timer = setInterval(this.getPhoto, 45000)
  }
}
</script>

<style>
@font-face {
  font-family: 'PermianSlabSerif-Typeface';
  src: local("/fonts/PermianSlabSerifTypeface.woff2");
}
#app{
  height: 100vh;
}
* {
  margin: 0;
  padding: 0;
}
.photo_content{
  align-content: center;
  height: 600px;
  width: 400px;
  margin-bottom: 10px;
  margin-top: 10px;
}
.wrapper {
  /*height: 150px;*/
  padding: 36px;
  background-color: #fff;
  flex-direction: row;
  display: flex;
  height: calc(100% - 72px);
}

.wrapper_video {
  background-color: #fff;
  /*height: 150px;*/
  flex: 1 1 70%;
  display: flex;
  flex-direction: column;
}

.wrapper_content {
  background-color: #c62e3e;
  /*height: 150px;*/
  flex: 1 1 30%;
}
video {
  height: 720px;
  width: 1280px;
}
.spacer {
  flex: 1 1 100%;
  display: block;
}
.wrapper_video__rss{
  /*background-color: #c62e3e;*/
  margin-bottom: 45px;
  margin-right: 3%;
  padding-bottom: 10px;
  font-family: PermianSlabSerif-Typeface,serif;
}
img {
  margin-right: 20px;
}
.wrapper_content__photo{
  padding-left: 12%;
  padding-right: 12%;
  padding-top: 20px;
}
.wrapper_content__time{
  padding-top: 10%;
  color: white;
}
.block_time{
  text-align: justify;
  margin-left: 13%;
  font-size: 7em;
}
.block_date{
  margin-top: 5%;
  margin-left: 30%;
  font-size: 3.0em;
}
.medved{
  position: absolute;
  padding-left: 0.5%;
  margin-top: -7%;
  width: 28%;
}
</style>
