<template>
  <div id="app">
    <div class="wrapper">
      <div class="wrapper_video">
        <div class="wrapper_video__video">
          <video ref="video" autoplay preload @ended="endedPlaying"/>
        </div>
        <div class="spacer"/>
        <div class="wrapper_video__runningstring">
          <marquee direction="left" scrollamount="20">{{ runningString }}</marquee>
        </div>
        <div class="spacer"/>
      </div>
      <div class="wrapper_content">
        <div class="wrapper_content__time">
          <h1 class="block_time">{{ ClockTime }}</h1>
          <!--          <img alt="" class="medved" src="../public/medved.png"/>-->
          <h2 class="block_date">{{ ClockDate }}</h2>
        </div>
        <hr class="hr__time_weather" />
        <div class="wrapper_content__weather">
          <div class="weather_title">Сейчас за окном</div>
          <ul>
            <li><div class="weather_cloud">Облачность: {{ weatherCloud }}</div></li>
            <li><div class="weather_temp">Температура: {{ weatherTemp }}&#176;</div></li>
            <li><div class="weather_fells">Ощущается как: {{ weatherFeel }}&#176;</div></li>
            <li><div class="weather_humidity">Влажность: {{ weatherHumidity }}%</div></li>
            <li><div class="weather_wind">Скорость ветра: {{ weatherWind }} км/ч</div></li>
          </ul>
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
      weatherFeel: null,
      weatherCloud: '',
      weatherWind: null,
      weatherHumidity: null,
      weather_timer: '',

      runningString: '',
      string_timer: ''
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
    getRunningString() {
      let axios = require('axios');
      axios.get('http://127.0.0.1:8000/getRunningString')
          .then((response) => {
            // handle success
            console.log(response.data);
            this.runningString = response.data.string
          });
      // console.log(this.urlVideo)
    },
    getWeather() {
      let axios = require('axios');
      axios.get('https://api.openweathermap.org/data/2.5/weather?lat=58.006817&lon=56.1860062&lang=ru&units=metric&appid=737f5257f034b715433c5446195947b4')
          .then((response) => {
            console.log(response);
            this.weatherTemp = response.data.main.temp;
            this.weatherFeel = response.data.main.feels_like;
            this.weatherWind = response.data.wind.speed;
            this.weatherCloud = response.data.weather[0].description;
            this.weatherHumidity = response.data.main.humidity;
          });
    },
    getNow: function() {
      [this.ClockDate, this.ClockTime] = ((l,d) => [d.toLocaleDateString(l).replace(/\./g, '-'), d.toLocaleTimeString(l).slice(0,-3)])('ru-RU', new Date());    },
    endedPlaying(){
      // console.log("ended");
      this.getVideo();
    }
  },
  mounted() {
    this.getVideo()
    this.getWeather()
    this.getRunningString()
    this.string_timer = setInterval(this.getRunningString, 60000)
    this.weather_timer = setInterval(this.getWeather, 60000)
    this.time_timer = setInterval(this.getNow, 1000)
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
.hr__time_weather{
  margin-top: 50px;
  margin-bottom: 50px;
  width: 280px;
  margin-left: 50px;
}
.wrapper {
  /*background-color: #c62e3e;*/
  background-color: #888;
  color: white;
  /*height: 150px;*/
  padding: 18px;
  flex-direction: row;
  display: flex;
  height: calc(100% - 36px);
}

.wrapper_video {
  background-color: #c62e3e;
  /*background-color: #888;*/
  /*height: 150px;*/
  flex: 1 1 1500px;
  display: flex;
  flex-direction: column;
}
.wrapper_content {
  background-color: #888;
  /*background-color: #c62e3e;*/
  /*height: 150px;*/
  flex: 1 1 420px;
}
video {
  /*height: 0px;*/
  width: 1500px;
}
.spacer {
  flex: 1 1 100%;
  display: block;
  background-color: #c62e3e;
  /*background-color: #888;*/
}
.wrapper_video__runningstring{
  /*background-color: #b51d0d;*/
  background-color: #c62e3e;
  margin-top: 5px;
  width: 1500px;
  padding-bottom: 20px;
  font-size: 3.2em;
  font-family: PermianSlabSerif-Typeface,serif;
}
img {
  margin-right: 20px;
}
.wrapper_content__weather {
  padding-left:19%;
  /*padding-right: 12%;*/
  font-size: 1.8em;
}
.weather_title {
  margin-left: -28px;
  font-size: 1.4em;
}
.wrapper_content__time{
  padding-top: 10%;
  margin-left: 50px;
  color: white;
}
.block_time{
  text-align: justify;
  /*margin-left: 13%;*/
  font-size: 7.4em;
}
.block_date{
  /*margin-top: 5%;*/
  /*margin-left: 30%;*/
  font-size: 3.8em;
}
</style>
