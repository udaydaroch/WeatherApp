<template>
  <div id="app">
    <h1>Weather Search</h1>
    <fieldset>
      <legend>Search for a location</legend>
      <input type="text" v-model="query" placeholder="Enter location" />
    </fieldset>
    <button @click="getWeather">Search</button>

    <div v-if="forecastData.length" class="forecast-container">
      <h2>7-Day Forecast for {{ forecastData[0].date }}</h2>
      <div class="forecast-cards">
        <div v-for="(day, index) in forecastData" :key="index" class="forecast-card">
          <h3>{{ day.date }}</h3>
          <img :src="'https:' + day.day.condition.icon" alt="Weather icon" />
          <p>Temperature: {{ day.day.avgtemp_c }}°C</p>
          <p>Condition: {{ day.day.condition.text }}</p>
          <p>Wind: {{ day.day.maxwind_kph }} kph</p>
          <p>Humidity: {{ day.day.avghumidity }}%</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { ref } from 'vue';

export default {
  name: 'App',
  setup() {
    const forecastData = ref([]);  // Store 7-day forecast data
    const query = ref("");
    const apikey = process.env.VUE_APP_API_KEY;

    const getWeather = () => {
      const url = `http://api.weatherapi.com/v1/forecast.json?key=${apikey}&q=${query.value}&days=7`;
      fetch(url)
        .then(response => response.json())
        .then(data => {
          console.log(data);  // Log full data for verification
          forecastData.value = data.forecast.forecastday;  // Store 7-day forecast data
        })
        .catch(error => {
          console.error("Error fetching weather data:", error);
          forecastData.value = [];  // Reset data if an error occurs
        });
    };

    return { forecastData, query, getWeather };
  }
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

fieldset {
  border: none;
  margin-bottom: 10px;
}

button {
  margin-top: 10px;
  padding: 10px 20px;
  font-size: 16px;
  cursor: pointer;
}

.forecast-container {
  margin-top: 20px;
  font-size: 24px;
  color: #2c3e50;
}

.forecast-cards {
  display: flex;
  overflow-x: auto;
  gap: 10px;
  padding: 10px;
}

.forecast-card {
  background-color: #f4f4f4;
  border-radius: 10px;
  padding: 15px;
  width: 200px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  text-align: center;
}

.forecast-card img {
  width: 50px;
  height: 50px;
}

.forecast-card h3 {
  font-size: 18px;
  margin-bottom: 10px;
}

.forecast-card p {
  font-size: 14px;
  margin: 5px 0;
}
</style>
