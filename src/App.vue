<template>
  <div id="app">
    <div class="wrapper">
      <div class="wrapper_top">
        <div class="wrapper_top_date">{{ ClockDate }}</div>
        <div class="wrapper_top_time">{{ ClockTime }}</div>
        <div class="wrapper_top_temp">{{ weatherTemp }}&#176;C</div>
      </div>
      <div class="wrapper_bottom">
        <div class="unavailable_text" :style="displayUnavailableText">Добро пожаловать!</div>
        <div class="wrapper_bottom_video" :style="displayWrapperBottom">
          <video ref="video" autoplay preload @ended="endedPlaying"/>
        </div>
        <div class="wrapper_bottom_sidebar">
          <div class="wrapper_bottom_sidebar_news_title" :style="displayWrapperBottom">Новость дня:</div>
          <div class="wrapper_bottom_sidebar_news" :style="displayWrapperBottom">{{ newsOfDay }}</div>
          <div class="wrapper_bottom_sidebar_logo" :style="displayLogo">
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

      newsOfDay: null,
      news_timer: '',

      displayLogo: 'display: block',
      displayWrapperBottom: 'display: block',
      displayUnavailableText: 'display: none',

      backend_timer: '',
    }
  },
  methods: {
    getVideo() {
      let axios = require('axios');
      axios.get('http://127.0.0.1:8000/getVideo')
          .then((response) => {
            // handle success
            // console.log(response.data);
            this.urlVideo = response.data.url
            this.$refs.video.src = response.data.url
          })
      console.log(this.urlVideo)
    },
    getWeather() {
      let axios = require('axios');
      axios.get('https://api.openweathermap.org/data/2.5/weather?lat=58.006817&lon=56.1860062&lang=ru&units=metric&appid=')
          .then((response) => {
            // console.log(response);
            this.weatherTemp = Math.round(response.data.main.temp);
            // console.log(this.weatherTemp, this.weatherTemp>0)
            if (this.weatherTemp>0){
              this.weatherTemp = '+' + this.weatherTemp.toString()
            }
          });
    },
    getNewsOfDay(){
      let axios = require('axios');
      axios.get('http://127.0.0.1:8000/getNewsOfDay')
          .then((response) => {
            this.newsOfDay = response.data.string;
            if (this.newsOfDay.length > 85){
              this.displayLogo = 'display: none'
            }
            else {
              this.displayLogo = 'display: block'
            }
          })
    },
    isRefreshNews(){
      if (this.ClockTime === '05:00'){
        this.getNewsOfDay();
      }
    },
    getNow() {
      [this.ClockDate, this.ClockTime] = ((l,d) => [d.toLocaleDateString(l).slice(0,-5), d.toLocaleTimeString(l).slice(0,-3)])('ru-RU', new Date());
      if (this.ClockDate[0] === '0'){
        this.ClockDate = this.ClockDate.slice(1,)
      }
    },
    endedPlaying(){
      // console.log("ended");
      this.getVideo();
    },
    isBackAvailable(){
      let axios = require('axios')
      axios.get('http://127.0.0.1:8000/')
          .then((response) => {
            console.log(response)
            if (this.displayWrapperBottom === 'display: none'){
              this.getVideo()
              this.displayWrapperBottom = 'display: block'
              this.displayUnavailableText = 'display: none'
              this.displayLogo = 'display: block; padding-left: 100px; padding-top: 50px'
              if (this.newsOfDay === null){
                this.getNewsOfDay()
              }
            }
          })
          .catch((error)=>{
            this.displayLogo = 'display: block; padding-left: 1380px; padding-top: 684px'
            this.displayWrapperBottom = 'display: none'
            this.displayUnavailableText = 'display: block'
            console.log(error)
      })
    },
  },
  mounted() {
    this.getVideo()
    this.getWeather()
    this.getNewsOfDay()
    this.isBackAvailable()
    this.backend_timer = setInterval(this.isBackAvailable, 2000)
    this.news_timer = setInterval(this.isRefreshNews, 60000)
    this.weather_timer = setInterval(this.getWeather, 60000)
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
.unavailable_text{
  font-family: PermianSansTypeface,sans-serif;
  font-style: normal;
  font-weight: normal;
  font-size: 195px;
  line-height: 63px;
  color: #C62E3E;
  padding-top: 340px;
  position: absolute;
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
  display: block;
  position: absolute;
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
