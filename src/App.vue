<script>
import axios from 'axios'
export default {
  data() {
    return {
      city: "",
      error: "",
      info: null,
    }
  },
  computed: {
    cityName() {
      return "'" + this.city +"'"
    },
    showTemp() {
      return "" + this.info.main.temp
    },
    showFeelsLike() {
      return "" + this.info.main.feels_like
    },
    showMinTemp() {
      return "" + this.info.main.temp_min
    },
    showMaxTemp() {
      return "" + this.info.main.temp_max
    },
  },
  methods: {
    getWeather () {
      if(this.city.trim().length < 2) {
        this.error = "incorrect city name"
        return false
      }

      this.error=''

      axios.get(`https://api.openweathermap.org/data/2.5/weather?q=${this.city}&units=metric&appid=dde9a829234e1c08a559768a8998d5df`)
        .then(res => (this.info = res.data ))
    }
  }
}
</script>

<template>
  <div class="wrapper">
    <div class="header">
      <div class="title">Weather app</div>
      <div class="found">Found out weather<br>in your city</div>
    </div>
    <div class="main">
      <div class="row-top">
        <div>View </div> 
        <div>the </div> 
        <div>weather</div>
        <div class="city">In</div>
      </div>
      <div class="row-bottom">
        <div class="city">In</div>
        <div class="write">
          <input type="city" v-model="city" placeholder="Write the city">
          <button class="btn" @click="getWeather()"><img src=".//assets/arrow-button.svg" alt="btn"></button>
        </div>
      </div>
    </div>
    <p class="error">{{ error }}</p>
    
    <div class="indicators" v-if="info !=null">
      <div class="indicators-left"><div class="indicators-found">Found weather in <br><span class="uppercase">{{ city == "" ? "your city" : cityName }}</span></div></div>
      <div class="indicators-right">
        <div class="indicators-title">
          <div>INDICATORS</div>
          <img src=".//assets/icon_celsius.svg" alt="celsius">
        </div>
        <div class="indicator-item">
          <div class="indicator-item__img"><img src=".//assets/icon_temperature.svg" alt=""></div>
          <div class="indicator-item__info item__info">
            <div class="item__info-name">Temperature</div>
            <div class="item__info-value">{{ showTemp }}</div>
          </div>
        </div>
        <div class="indicator-item">
          <div class="indicator-item__img"><img src=".//assets/icon_feel.svg" alt=""></div>
          <div class="indicator-item__info">
            <div class="item__info-name">Feels like</div>
            <div class="item__info-value">{{ showFeelsLike }}</div>
          </div>
        </div>
        <div class="indicator-item">
          <div class="indicator-item__img"><img src=".//assets/icon_min.svg" alt=""></div>
          <div class="indicator-item__info">
            <div class="item__info-name">Min temperature</div>
            <div class="item__info-value">{{ showMinTemp }}</div>
          </div>
        </div>
        <div class="indicator-item">
          <div class="indicator-item__img"><img src=".//assets/icon_max.svg" alt=""></div>
          <div class="indicator-item__info">
            <div class="item__info-name">Max temperature</div>
            <div class="item__info-value">{{ showMaxTemp }}</div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style>
  .indicators {
    margin-top: 50px;
    display: flex;
    justify-content: space-between;
  }


  .indicators-right {
    display: flex;
    flex-direction: column;
    align-items: flex-start;
    max-width: 500px;
    width: 40%;
  }



  .indicators-found {
    font-size: 12px;
    line-height: 15px;
  }

  .uppercase {
    text-transform: uppercase;
    font-style: italic;
  }

  .indicators-title {
    display: flex;
    justify-content: space-between;
    width: 100%;
    opacity: 40%;
  }

  .indicator-item {
    display: flex;
    width: 100%;
    padding: 20px 0;
    border-bottom: 1px solid rgba(0,0,0,0.4);
  }

  .indicator-item:last-child {
    border-bottom: 0;
  }

  .indicator-item__img {
    width: 5%;
  }

  .indicator-item__info {
    display: flex;
    justify-content: space-between;
    width: 100%;
  }

  .indicator-info {
    font-size: 16px;
    line-height: 19px;
  }

  .main {
    display: flex;
    flex-direction: column;
  }

  .row-top {
    display: flex;
    justify-content: space-between;
    gap: 5%;
    margin: 15vh 0 0 0;
    font-size: 128px;
    line-height: 155px;
  }
  .row-top > .city {
    display: none;
  }


  .row-bottom {
    display: flex;
    justify-content: flex-start;
    align-items: center;
    gap: 5%;
    font-size: 128px;
    line-height: 155px;
    position: relative;
  }

  .write {
    opacity: 0.4;
    transition: opacity 0.5s ease;
    padding: 20px 0 20px 20px;
  }

  .write:hover {
    opacity: 1;
  }


  .error {
    color: red;
    text-align: center;
  }

  .wrapper {
    overflow: hidden;
    padding: 60px 60px 0 60px;
  }

  .write > input {
    background: transparent;
    border: 0;
    border-bottom: 2px solid #110813;
    font-size: 128px;
    line-height: 155px;
    padding: 20px 0;
    outline: none;
    transition: 500ms ease;
    width: 100%;
  }
  @media (max-width: 1200px){
    .write > input,
    .row-bottom,
    .row-top {
      font-size: 100px;
      line-height: 115px;
    }
  }

  @media (max-width: 1024px){
    .write > input,
    .row-bottom,
    .row-top {
      font-size: 80px;
      line-height: 90px;
    }
    .indicators-right {
      width: 60%;
    }
    .btn > img {
      width: 70%;
      float: right;
    }
  }

  @media (max-width: 768px){
    .write > input,
    .row-bottom,
    .row-top {
      font-size: 60px;
      line-height: 66px;
    }
  }

  @media (max-width: 640px){
    .row-bottom,
    .row-top {
      font-size: 48px;
      line-height: 50px;
    }
    .write > input {
      font-size: 40px;
      line-height: 40px;
      padding: 0;
    }
  }

  @media (max-width: 524px){
    .row-bottom,
    .row-top {
      font-size: 60px;
      line-height: 66px;
    }
    .indicators {
      margin-top: 30px;
      flex-direction: column;
      gap: 30px;
      align-items: center;
    }
    .indicators-found {
      display: flex;
      align-items: center;
      flex-direction: column;
    }
    .indicators-left {
      opacity: 0.3;
    }
    .indicators-right {
      width: 100%;
    }
  }

  @media (max-width: 400px){
    .row-bottom,
    .row-top {
      font-size: 48px;
      line-height: 50px;
    }
    .write > input {
      font-size: 40px;
      line-height: 40px;
    }
  }


  input::placeholder {
    color: #1E1E1E;
    font-weight: 500;
    font-size: 16px;
    line-height: 19px;
    text-transform: uppercase;
  }

  .header {
    font-size: 12px;
    line-height: 15px;
    display: flex;
    justify-content: flex-start;
    gap: 5%;
  }

  .title {
    text-transform: uppercase;
    width: 110px;
  }
  .btn {
    position: absolute;
    right: 0;
    top: 50%;
    color: #1E1E1E;
    cursor: pointer;
    transition: 500ms ease;
    background-color: transparent;
    border: 0;
  }

  .btn:hover {
    opacity: 1;
    transform: scale(1.1) translateY(-5px);
  }

  @media (max-width: 1024px){
    .btn {
      top: 40%;
    }
  }
  @media (max-width: 640px) {
    .wrapper {
      padding: 20px;
    }
    .header {
      justify-content: space-between;
      width: 100%;
    }
    .title {
      width: auto;
    }
    .city {
      display: none;
    }
    .row-top {
      flex-wrap: wrap;
    }
    .row-top > .city {
      display: block;
    }
    .write {
      width: 100%;
      padding: 0;
    }
    .btn {
      float: right;
      top: 50%;
    }
    .btn > img {
      width: 30%
    }
  }

</style>
