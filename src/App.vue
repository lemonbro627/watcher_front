<template>
  <div id="app">
    <div class="wrapper">
      <div class="wrapper_top">
        <div class="wrapper_top_date">{{ ClockDate }}</div>
        <div class="wrapper_top_time">{{ ClockTime }}</div>
        <div class="wrapper_top_temp">{{ weatherTemp }}&#176;C</div>
      </div>
      <div class="wrapper_bottom">
        <div class="wrapper_bottom_video">
          <video ref="video" autoplay preload @ended="endedPlaying"/>
        </div>
        <div class="wrapper_bottom_sidebar">
          <div class="wrapper_bottom_sidebar_news_title">Новость дня:</div>
          <div class="wrapper_bottom_sidebar_news">С 1 октября в университете появится Яндекс.Такси</div>
          <div class="wrapper_bottom_sidebar_logo">
            <img src="../public/company.png" alt="" class="company_logo"/>
          </div>
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
      ClockDate: null,
      ClockTime: null,
      time_timer: '',

      weatherTemp: null,
      weather_timer: '',
    }
  },
  methods: {
    getVideo() {
      let axios = require('axios');
      axios.get('http://127.0.0.1:8000/getVideo')
          .then((response) => {
            // handle success
            console.log(response.data);
            this.urlVideo = response.data.url
            this.$refs.video.src = response.data.url
          });
      // console.log(this.urlVideo)
    },
    getWeather() {
      let axios = require('axios');
      axios.get('https://api.openweathermap.org/data/2.5/weather?lat=58.006817&lon=56.1860062&lang=ru&units=metric&appid=')
          .then((response) => {
            // console.log(response);
            this.weatherTemp = response.data.main.temp;
          });
    },
    getNow: function() {
      [this.ClockDate, this.ClockTime] = ((l,d) => [d.toLocaleDateString(l).slice(0,-5), d.toLocaleTimeString(l).slice(0,-3)])('ru-RU', new Date());    },
    endedPlaying(){
      // console.log("ended");
      this.getVideo();
    }
  },
  mounted() {
    this.getVideo()
    this.getWeather()
    this.weather_timer = setInterval(this.getWeather, 3600000)
    this.time_timer = setInterval(this.getNow, 1000)
  }
}
</script>
<style>
@font-face {
  font-family: 'HalvarBreit-Md';
  src: url("../public/fonts/HalvarBreit-Md.ttf") format('truetype');
}
@font-face {
  font-family: 'HalvarBreit-Th';
  src: url("../public/fonts/HalvarBreit-Th.ttf") format('truetype');
}
@font-face {
  font-family: 'PermianSansTypeface';
  src: url("../public/fonts/PermianSansTypeface.ttf") format('truetype');
}
#app{
  height: 100vh;
}
* {
  margin: 0;
  padding: 0;
}
.wrapper_top{
  display: flex;
  flex-direction: row;
}
.wrapper_top_date{
  width: 500px;
  height: 139px;
  top: 81px;
  left: 50px;
  font-family: HalvarBreit-Th,sans-serif;
  font-style: normal;
  font-weight: normal;
  font-size: 120px;
  line-height: 139px;
  display: flex;
  color: #C62E3E;
}
.wrapper_top_time{
  width: 620px;
  height: 180px;
  left: 649px;
  top: 63px;
  padding-left: 100px;
  padding-right: 100px;
  font-family: HalvarBreit-Md,sans-serif;
  font-style: normal;
  font-weight: normal;
  font-size: 140px;
  line-height: 162px;
  text-align: center;
  color: #C62E3E;
}
.wrapper_top_temp{
  width: 500px;
  height: 139px;
  left: 1380px;
  top: 81px;
  font-family: HalvarBreit-Th,sans-serif;
  font-style: normal;
  font-weight: normal;
  font-size: 120px;
  line-height: 139px;
  text-align: right;
  color: #C62E3E;
}
.wrapper_bottom_video {
  width: 1280px;
  height: 720px;
  padding-top: 80px;
}
.wrapper_bottom{
  flex-direction: row;
  display: flex;
}
.wrapper_bottom_sidebar_news_title{
  padding-left: 100px;
  font-family: PermianSansTypeface,sans-serif;
  font-style: normal;
  font-weight: normal;
  font-size: 50px;
  line-height: 63px;
  color: #C62E3E;
  padding-top: 80px;
}
.wrapper_bottom_sidebar_news{
  padding-left: 100px;
  padding-top: 40px;
  font-family: PermianSansTypeface, sans-serif;
  font-style: normal;
  font-weight: bold;
  font-size: 50px;
  line-height: 63px;
  color: #C62E3E;
  height: 450px;
}
.wrapper_bottom_sidebar_logo{
  padding-left: 100px;
  padding-top: 50px;
  width: 440px;
}
.company_logo{
  width: 440px;
}
.wrapper {
  background-color: #fff;
  color: white;
  padding: 50px;
  flex-direction: column;
  display: flex;
}
</style>
