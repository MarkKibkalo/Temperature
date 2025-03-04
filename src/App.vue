<script>
import axios from 'axios';
export default {
  data() {
    return {
      city: '',
      error: '',
      info: null,
      language: 'en',
      translations: {
        en: {
          'Weather app': 'Weather app',
          'Found out weather': 'Found out weather',
          'in your city': 'in your city',
          'Write the city': 'Write the city',
          'City not found': 'City not found',
          Temperature: 'Temperature',
        },
        uk: {
          'Weather app': 'Прогноз погоди',
          'Found out weather': 'Дізнайтеся погоду',
          'in your city': 'у вашому місті',
          'Write the city': 'Введіть місто',
          'City not found': 'Місто не знайдено',
          Temperature: 'Температура',
          INDICATORS: 'ІНДІКАТОРИ',
          View: 'Дізнатись',
          the: ' ',
          weather: 'погоду',
          in: ' ',
          'Feels like': 'Відчувається як',
          'Min temperature': 'Мінімальна температура',
          'Max temperature': 'Максимальна температура',
          'Found weather in': 'Знайдено погоду у місті',
        },
      },
    };
  },
  computed: {
    cityName() {
      return this.city ? `'${this.city}'` : '';
    },
    indicators() {
      return {
        Temperature: 'Temperature',
        'Feels like': 'Feels like',
        'Min temperature': 'Min temperature',
        'Max temperature': 'Max temperature',
      };
    },
    indicatorValues() {
      return {
        Temperature: this.info ? this.info.main.temp : '',
        'Feels like': this.info ? this.info.main.feels_like : '',
        'Min temperature': this.info ? this.info.main.temp_min : '',
        'Max temperature': this.info ? this.info.main.temp_max : '',
      };
    },
  },
  methods: {
    t(key) {
      return this.translations[this.language][key] || key;
    },
    setLanguage(lang) {
      this.language = lang;
    },
    async translateCity(city) {
      try {
        const response = await axios.get('https://api.mymemory.translated.net/get', {
          params: {
            q: city,
            langpair: 'uk|en',
          },
        });
        return response.data.responseData.translatedText;
      } catch (error) {
        console.error('Translation error:', error);
        return city;
      }
    },
    async getWeather() {
      if (this.city.trim().length < 2) {
        this.error = this.t('incorrect city name');
        return;
      }
      this.error = '';
      const translatedCity = await this.translateCity(this.city);
      axios
        .get(
          `https://api.openweathermap.org/data/2.5/weather?q=${translatedCity}&units=metric&appid=dde9a829234e1c08a559768a8998d5df`,
        )
        .then((res) => (this.info = res.data))
        .catch(() => (this.error = this.t('City not found')));
    },
  },
};
</script>

<template>
  <div class="wrapper">
    <div class="header">
      <div class="container">
        <div class="title">{{ t('Weather app') }}</div>
        <div class="found">{{ t('Found out weather') }}<br />{{ t('in your city') }}</div>
      </div>
      <div class="language-switcher">
        <button
          class="btn-language"
          :class="{ active: language === 'en' }"
          @click="setLanguage('en')">
          EN
        </button>
        <button
          class="btn-language"
          :class="{ active: language === 'uk' }"
          @click="setLanguage('uk')">
          UA
        </button>
      </div>
    </div>

    <div class="main">
      <div class="row-top">
        <div>{{ t('View') }}</div>
        <div>{{ t('the') }}</div>
        <div>{{ t('weather') }}</div>
        <div>{{ t('in') }}</div>
      </div>
      <div class="row-bottom">
        <div class="write">
          <input type="city" v-model="city" :placeholder="t('Write the city')" />
          <button class="btn" @click="getWeather()">
            <img src=".//assets/arrow-button.svg" alt="btn" />
          </button>
        </div>
      </div>
    </div>
    <p class="error">{{ error }}</p>

    <div class="indicators" v-if="info != null">
      <div class="indicators-left">
        <div class="indicators-found">
          {{ t('Found weather in') }} <br />
          <span class="uppercase">{{ city == '' ? t('your city') : cityName }}</span>
        </div>
      </div>
      <div class="indicators-right">
        <div class="indicators-title">
          <div>{{ t('INDICATORS') }}</div>
          <img src="./assets/icon_celsius.svg" alt="celsius" />
        </div>
        <div class="indicator-item" v-for="(label, key) in indicators" :key="key">
          <!-- <div class="indicator-item__img">
            <img :src="`./assets/icon_${key}.svg`" alt="{key}" />
          </div> -->
          <div class="indicator-item__info">
            <div class="item__info-name">{{ t(label) }}</div>
            <div class="item__info-value">{{ indicatorValues[key] }}</div>
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
  border-bottom: 1px solid rgba(0, 0, 0, 0.4);
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
  justify-content: flex-start;
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
  font-size: 128px;
  line-height: 155px;
  position: relative;
}

.write {
  opacity: 0.4;
  transition: opacity 0.5s ease;
  padding: 20px 0;
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
@media (max-width: 1200px) {
  .write > input,
  .row-bottom,
  .row-top {
    font-size: 100px;
    line-height: 115px;
  }
}

@media (max-width: 1024px) {
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

@media (max-width: 768px) {
  .write > input,
  .row-bottom,
  .row-top {
    font-size: 60px;
    line-height: 66px;
  }
}

@media (max-width: 640px) {
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

@media (max-width: 524px) {
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

@media (max-width: 400px) {
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
  color: #1e1e1e;
  font-weight: 500;
  font-size: 16px;
  line-height: 19px;
  text-transform: uppercase;
}

.header {
  font-size: 12px;
  line-height: 15px;
  display: flex;
  justify-content: space-between;
  gap: 5%;
}

.container {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.title {
  text-transform: uppercase;
  width: 110px;
}
.btn {
  position: absolute;
  right: 0;
  top: 50%;
  color: #1e1e1e;
  cursor: pointer;
  transition: 500ms ease;
  background-color: transparent;
  border: 0;
}

.language-switcher {
  display: flex;
  gap: 5px;
  align-items: flex-start;
}

.btn-language {
  padding: 10px;
  border-radius: 8px;
  border: 0;
  cursor: pointer;
}

.btn-language.active {
  background-color: #000000;
  color: #ffffff;
}

.btn:hover {
  opacity: 1;
  transform: scale(1.1) translateY(-5px);
}

@media (max-width: 1024px) {
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
    width: 30%;
  }
}
</style>
