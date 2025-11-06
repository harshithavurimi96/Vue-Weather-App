<template>
  <div :class="['app', backgroundClass]">
    <div class="weather-container">
      <h1>🌤️ Weather App</h1>

      <input
        v-model="city"
        placeholder="Enter city name"
        @keyup.enter="getWeather"
      />
      <button @click="getWeather">Get Weather</button>

      <div v-if="weather">
        <h2>{{ weather.name }}, {{ weather.sys.country }}</h2>
        <img
          class="weather-icon"
          :src="`https://openweathermap.org/img/wn/${weather.weather[0].icon}@2x.png`"
          :alt="weather.weather[0].description"
        />
        <p>🌡️ {{ Math.round(weather.main.temp) }}°C</p>
        <p>{{ weather.weather[0].description }}</p>
      </div>

      <div v-if="error" style="color:red; margin-top:10px;">
        {{ error }}
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'


const API_KEY = '1e6e5c18e52b21999d589b810b4342de'

const city = ref('')
const weather = ref(null)
const error = ref(null)

async function getWeather() {
  if (!city.value) {
    error.value = 'Please enter a city name.'
    weather.value = null
    return
  }

  try {
    error.value = null
    const response = await fetch(
      `https://api.openweathermap.org/data/2.5/weather?q=${city.value}&appid=${API_KEY}&units=metric`
    )
    if (!response.ok) throw new Error('City not found')
    const data = await response.json()
    weather.value = data
  } catch (err) {
    error.value = err.message
    weather.value = null
  }
}


const backgroundClass = computed(() => {
  if (!weather.value) return 'default-bg'
  const condition = weather.value.weather[0].main.toLowerCase()
  if (condition.includes('cloud')) return 'cloudy'
  if (condition.includes('rain')) return 'rainy'
  if (condition.includes('snow')) return 'snowy'
  if (condition.includes('clear')) return 'sunny'
  return 'default-bg'
})
</script>

<style scoped>

</style>
