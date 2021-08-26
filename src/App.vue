<template>
  <div id="app" v-if="weather" :class="weather.weather[0].main.toLowerCase()"> 
    <div v-if="isLoading" id="loader"><img src="./assets/loader.gif" alt=""></div>
    <main>
      <h1>the.weather</h1>
      <div class="weather-wrapper">
        <div class="temp">{{Math.round(weather.main.temp)}}°</div>
        <div class="location-box">
          <div class="location">{{weather.name}}</div>
          <div class="date-wrapper">
            <div class="time"><p>{{time}}</p></div>
            <div class="date"><p>{{dateBuilder}}</p></div>
          </div>
        </div>
        <div class="weather-box">
          <img :src="`http://openweathermap.org/img/w/${icon}.png`" alt="icon">
          <p>{{weather.weather[0].main}}</p>
        </div>
      </div>
    </main>
    <div id="sidebar">
      <div class="sidebar-wrapper">
        <div class="search-box">
          <div class="search-input">
            <input @click="toggleError" @keydown="toggleError" @keypress.enter="fetchWeather(query)" v-model="query" type="text" class="search-bar" placeholder="Another location">
            <small v-if="error">Input field is empty</small>
            <small v-if="notFound">City not found</small>
          </div>
          <div @click="fetchWeather(query)" class="search-btn">
            <img src="./assets/search.png" alt="">
          </div>
        </div>
        <div class="regions">
          <ul>
            <li @click="fetchWeather('tashkent')">Tashkent</li>
            <li @click="fetchWeather('bukhara')">Bukhara</li>
            <li @click="fetchWeather('andijan')">Andijan</li>
            <li @click="fetchWeather('khiva')">Khiva</li>
            <li @click="fetchWeather('shahrisabz')">Shahrisabz</li>
            <li @click="fetchWeather('zomin')">Zomin</li>
            <li @click="fetchWeather('guliston')">Guliston</li>
            <li @click="fetchWeather('nukus')">Nukus</li>
            <li @click="fetchWeather('namangan')">Namangan</li>
            <li @click="fetchWeather('qarshi')">Qarshi</li>
            <li @click="fetchWeather('termez')">Termez</li>
            <li @click="fetchWeather('fergana')">Fergana</li>
          </ul>
        </div>
        <hr>
        <div v-if="weather" class="details">
          <h4>Weather Details</h4>
          <div class="details-wrapper">
            <div>
              <p>Cloudy</p><p>{{weather.clouds.all}} %</p>
            </div>
            <div>
              <p>Humidity</p><p>{{weather.main.humidity}} %</p>
            </div>
            <div>
              <p>Wind</p><p>{{weather.wind.speed}} km/h</p>
            </div>
            <div>
              <p>Pressure</p><p>{{weather.main.pressure}} hPa</p>
            </div>
          </div>
        </div>
        <hr>
      </div>
    </div>
  </div>
</template>

<script>

export default {
  data(){
    return{
      api_key: '6ff249d36a983ba8b94214f75eb360a4',
      url: 'https://api.openweathermap.org/data/2.5/',
      query: '',
      icon: '',
      weather: null,
      time: '',
      error: false,
      notFound: false,
      isLoading: false
    }
  },
  methods:{
    toggleError(){
      if(this.error === true || this.notFound === true)
      this.error = false
      this.notFound = false
    },
    fetchWeather(query){
      if(query !== ''){
        this.isLoading = true
        fetch(`${this.url}weather?q=${query}&units=metric&APPID=${this.api_key}`)
        .then(res => {
          return res.json();
        }).then(this.setResults)
      }else{
        this.error = true
      }
    },
    setResults(results){
      if(results.cod != '404'){
        this.isLoading = false
        this.error = false
        this.weather = results
        console.log(results)
        this.icon = this.weather.weather[0].icon
        this.query = ''
      }else{
        this.notFound = true
      }
    },
    zeroPadding(num, digit) {
    let zero = '';
    for(let i = 0; i < digit; i++) {
        zero += '0';
    }
    return (zero + num).slice(-digit);
    },
    updateTime() {
    let d = new Date();
    this.time = this.zeroPadding(d.getHours(), 2) + ':' + this.zeroPadding(d.getMinutes(), 2) //+ ':' + this.zeroPadding(d.getSeconds(), 2);
    // this.time = d.getHours() + ':' + d.getMinutes()
    }     
  },
  computed:{
    dateBuilder () {
      let d = new Date();
      let months = ["Jan", "Feb", "Mar", "Apr", "May", "Jun", "Jul", "Aug", "Sep", "Oct", "Nov", "Dec"];
      let days = ["Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"];
      
      let day = days[d.getDay()];
      let date = d.getDate();
      let month = months[d.getMonth()];
      let year = d.getFullYear().toString();
      return `${day} ${date} ${month} ${year.substring(2,4)}`;
    },
    showTime(){
      let today = new Date();
      let hour = today.getHours();
      let min = today.getMinutes();
      let sec = today.getSeconds();
        return `${hour}:${min}${sec} `
    }
  },
  created(){
    setInterval(this.updateTime, 1000);
    this.fetchWeather('tashkent')
  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Poppins&display=swap');
/* width */
::-webkit-scrollbar {
  width: 6px;
}
 
/* Handle */
::-webkit-scrollbar-thumb {
  background: rgba(149, 165, 166, 0.6);
  border-radius: 10px;
}

/* Handle on hover */
::-webkit-scrollbar-thumb:hover {
  background: rgba(149, 165, 166, 1); 
}

*{
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
body{
  font-family: 'Poppins', sans-serif;
}
hr{
  background-color:hsla(0,0%,100%,.6);;
  height: 0.5px; 
  border: none;
  margin: 12px 0;
}
#app{
  display: flex;
  width: 100%;
  background: linear-gradient(to right, #a1c4fd, #c2e9fb); 
  position: relative;
  z-index: 10; 
  /* overflow: hidden; */
}
#app.clear{
  background: url('./assets/clear.jpg') no-repeat center / cover; 
}
#app.clouds{
  background: url('./assets/clouds.jpg') no-repeat center / cover; 
}
#app.rain{
  background: url('./assets/rain.jpg') no-repeat center / cover; 
}
#app.thunderstorm{
  background: url('./assets/thunderstorm.jpg') no-repeat center / cover; 
}
#app.drizzle{
  background: url('./assets/drizzle.jpg') no-repeat center / cover; 
}
#app.snow{
  background: url('./assets/snow.jpg') no-repeat center / cover; 
}
main{
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  height: 100vh !important;
  width: 67%;
  padding: 60px 80px 30px;
  color: #fff;
}
.weather-wrapper{
  display: flex;
  align-items: center;
  position: relative;
}
.weather-wrapper .temp{
  display: flex;
  align-items: flex-end;
  font-size: 8rem;
}
.location-box{
  display: flex;
  flex-direction: column;
  justify-content:flex-end;
  margin-left: 2rem;
  margin-bottom: 1rem;
}
.location-box .location{
  font-size: 4rem;
}
.date-wrapper{
  display: flex;
}
.date-wrapper p{
  font-size: 1.2rem;
  margin-right: 0.6rem;
}
.weather-box{
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  position: relative;
  margin-bottom: 2.4rem;
}
.weather-box img{
  width: 120px;
}
.weather-box p{
  font-size: 1.2rem;
}
#sidebar{
  width: 33%;
  background-image: linear-gradient(to bottom,rgba(91, 62, 62, 0.3), rgba(0, 0, 1, 0.4));
  position: relative;
  z-index: 10;
}
.sidebar-wrapper{
  width: 85%;
  margin: 0 auto;
}
.search-box{
  display: flex;
  justify-content: space-between;
  height: 100px;
  margin-bottom: 40px;
}
.search-input{
  position: relative;
  align-self: flex-end;
  flex: 2;
}
.search-input small{
  position: absolute;
  left: 0;
  bottom: -30%;
  color: rgb(174, 21, 21);
  font-weight: bold;
}
.search-input input{
  height: 100px;
  width: 80%;
  border: none;
  outline: none;
  font-size: 1.1rem;
  color: hsla(0,0%,100%,1);
  padding-top: 40px;
  border-bottom: 0.7px solid #CCCDC6;
  background: transparent;
}
.search-input input::placeholder{
  color: hsla(0,0%,100%,.6);
}
.search-btn{
  position: absolute;
  top: 0;
  right: 0;
  height: 100px;
  background: rgba(170,205,225,0.5);
  transition: background 0.3s ease;
  padding: 32px;
  cursor: pointer;
}
.search-btn:hover{
  background: rgba(170,205,225,1);
}
.search-btn img{
  width: 100%;
  height: 100%;
}
.regions{
  cursor: pointer;
  height: 300px;
  overflow-y: scroll;
}
.regions ul{
  list-style: none;
}
.regions ul li{
  color: hsla(0,0%,100%,.6);
  padding: 13px 0
}
.regions ul li:hover,
.details h4{
  color: hsla(0,0%,100%,1);
}
.details-wrapper{
  display: flex;
  flex-direction: column;
}
.details-wrapper div{
  display: flex;
  justify-content: space-between;
  margin: 13px 0;
  color: hsla(0,0%,100%,.6);;
}
#loader{
  position: absolute;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  background: linear-gradient(to right, #DAE2F8, #D6A4A4);
  z-index: 100;
}
#loader img{
  position: relative;
  top: 50%;
  left: 50%;
  transform: translate(-50%,-50%);
}
/* media quaries */
@media screen and (max-width: 1220px) {
  h1{
    display: none;
  }
  #app{
    flex-direction: column;
  }
  main{
    justify-content: center;
    align-self: center;
    padding: 0;
  }
  .weather-wrapper .temp{
    font-size: 6rem;
  }
  #sidebar{
    width: 100%;
  }
}
@media screen and (max-width: 765px) {
  .weather-wrapper{
    flex-direction: column;
  }
  .weather-wrapper .temp,
  .date-wrapper{
    justify-self: center;
    align-self: center;
  }
  .location-box,
  .weather-box{
    margin: 0;
  }
}
</style>
