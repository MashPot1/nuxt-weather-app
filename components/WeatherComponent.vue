<script setup>
import { defineProps } from "vue";

const { weatherData } = defineProps({
  weatherData: Object,
});
</script>

<template>
  <div v-if="Object.keys(weatherData).length > 0" class="weatherCard">
    <p>{{ weatherData.name }}</p>
    <p>Weather: {{ Math.round(weatherData.main.temp * 10) / 10 }} °C</p>
    <p>
      Feels Like: {{ Math.round(weatherData.main.feels_like * 10) / 10 }} °C
    </p>
    <SVGCloudyIcon v-if="weatherData.weather[0].main == 'Clouds'" />
    <SVGRainIcon v-if="weatherData.weather[0].main == 'Rain'" />
    <SVGClearIcon v-if="weatherData.weather[0].main == 'Clear'" />
    <SVGSnowIcon v-if="weatherData.weather[0].main == 'Snow'" />
    <p>{{ weatherData.wind.speed }} m/s</p>
  </div>
</template>
