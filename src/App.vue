<script lang="ts">
import axios from 'axios'
import { countriesData } from './components/country-data'

interface WeatherData {
  location: {
    name: string
    region: string
    country: string
    lat: number
    lon: number
    tz_id: string
    localtime_epoch: number
    localtime: string
  }
  current: {
    last_updated_epoch: number
    last_updated: string
    temp_c: number
    temp_f: number
    is_day: number
    condition: {
      text: string
      icon: string
      code: number
    }
    wind_mph: number
    wind_kph: number
    wind_degree: number
    wind_dir: string
    pressure_mb: number
    pressure_in: number
    precip_mm: number
    precip_in: number
    humidity: number
    cloud: number
    feelslike_c: number
    feelslike_f: number
    vis_km: number
    vis_miles: number
    uv: number
    gust_mph: number
    gust_kph: number
  }
}

interface Countries {
  name: string
  code: string
}

export default {
  data() {
    return {
      weatherData: {} as WeatherData,
      city: 'Latvia',
      searchTerm: '',
      countries: countriesData,
      isFarenheit: false,
      loading: false,
      error: ''
    }
  },
  created() {
    axios
      .get<WeatherData>(
        `https://api.weatherapi.com/v1/current.json?key=ffc52c8cbd3641978f1123150231403&q=${this.city}&aqi=no`
      )
      .then(({ data }) => {
        this.weatherData = data
      })
  },
  computed: {
    filteredCountries() {
      return this.countries.filter((country: Countries) =>
        country.name.toLowerCase().includes(this.searchTerm.toLowerCase())
      )
    }
  },

  methods: {
    getWeatherData(props: string) {
      axios
        .get<WeatherData>(
          `https://api.weatherapi.com/v1/current.json?key=ffc52c8cbd3641978f1123150231403&q=${props}&aqi=no`
        )
        .then(({ data }) => {
          this.weatherData = data
        })
    },
    handleClick(country: string) {
      this.getWeatherData(country)
    },
    toggleCheckbox() {
      this.isFarenheit = !this.isFarenheit
    }
  }
}
</script>

<template>
  <div class="container">
    <h1 class="is-size-3 has-text-centered mb-3 mt-5 has-text-weight-semibold header">
      Weather App
    </h1>
    <div class="weather-grid">
      <div class="field">
        <label class="label" for="search-input">Specify country:</label>
        <div class="control specify-length">
          <input
            id="search-input"
            v-model="searchTerm"
            type="search"
            placeholder="Country.."
            class="input mb-2"
          />
        </div>
        <ul v-if="searchTerm.length > 0">
          <li
            class="list--remove-bullets add-pointer"
            v-for="country in filteredCountries.slice(0, 15)"
            :key="country.code"
            @click="handleClick(country.name)"
          >
            {{ country.name }}
          </li>
        </ul>
      </div>
      <div>
        <h2 class="label">Location data:</h2>
        <ul class="list--remove-bullets" v-if="weatherData.current">
          <li>
            <img
              :src="weatherData.current.condition.icon"
              class="image-size"
              alt="Weather condition"
            />
          </li>
          <li>
            Country: <b>{{ weatherData.location.country }}</b>
          </li>
          <li>
            City: <b>{{ weatherData.location.name }}</b>
          </li>

          <li v-if="isFarenheit">
            Temperature: <b>{{ weatherData.current.temp_f }}</b> &#8457;
          </li>
          <li v-else>
            Temperature: <b>{{ weatherData.current.temp_c }} </b> &#8451;
          </li>

          <li v-if="isFarenheit">
            Wind speed: <b>{{ weatherData.current.wind_mph }} </b>mp/h
          </li>
          <li v-else>
            Wind speed: <b>{{ weatherData.current.wind_kph }} </b>km/h
          </li>

          <li v-if="weatherData.current.humidity">
            Humidity: <b>{{ weatherData.current.humidity }}%</b>
          </li>
          <li v-if="weatherData.location.localtime">
            Date: <b>{{ weatherData.location.localtime.substring(0, 10) }}</b>
          </li>
        </ul>
        <div class="checkbox-container">
          <label class="switch">
            <input type="checkbox" v-model="isFarenheit" @click="toggleCheckbox" />
            <span class="slider round"></span>
          </label>
          <span v-if="isFarenheit"> Toggle to Celsius </span>
          <span v-else> Toggle to Farenheit </span>
        </div>
      </div>
    </div>
  </div>
</template>
<style scoped>
/* The switch - the box around the slider */
.switch {
  position: relative;
  display: inline-block;
  width: 60px;
  height: 34px;
}

/* Hide default HTML checkbox */
.switch input {
  opacity: 0;
  width: 0;
  height: 0;
}

/* The slider */
.slider {
  position: absolute;
  cursor: pointer;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: #ccc;
  -webkit-transition: 0.4s;
  transition: 0.4s;
}

.slider:before {
  position: absolute;
  content: '';
  height: 26px;
  width: 26px;
  left: 4px;
  bottom: 4px;
  background-color: white;
  -webkit-transition: 0.4s;
  transition: 0.4s;
}

input:checked + .slider {
  background-color: #2196f3;
}

input:focus + .slider {
  box-shadow: 0 0 1px #2196f3;
}

input:checked + .slider:before {
  -webkit-transform: translateX(26px);
  -ms-transform: translateX(26px);
  transform: translateX(26px);
}

/* Rounded sliders */
.slider.round {
  border-radius: 34px;
}

.slider.round:before {
  border-radius: 50%;
}

.checkbox-container {
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 10px 10px 0px 0px;
}

.text {
  margin-left: 10px;
}

.specify-length {
  width: 70%;
}

.image-size {
  width: 100px;
}

.header {
  font-size: 70px;
  font-weight: 600;
  background-image: linear-gradient(to left, #553c9a, #b393d3);
  color: transparent;
  background-clip: text;
  -webkit-background-clip: text;
}
</style>
