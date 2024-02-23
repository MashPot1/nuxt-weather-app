<script setup>
import { defineProps } from "vue";

const { forecastData } = defineProps({
  forecastData: Object,
});

const formatDate = (dt_txt) => {
  const dateTimeParts = dt_txt.split(" ");
  const timePart = dateTimeParts[1];
  const datePart = dateTimeParts[0];

  const time = timePart.substr(0, 5);
  const [year, month, day] = datePart.split("-");

  return `${day}.${month} ${time}`;
};

</script>

<template>
  <div v-if="forecastData.length > 0">
    <h2>Forecast Data:</h2>
    <ul class="forecastCardList">
      <li v-for="item in forecastData" :key="item.dt" class="forecastCard">
        <p>{{ formatDate(item.dt_txt) }}</p>
        <p>{{ Math.round(item.main.temp * 10) / 10 }} °C</p>
        <SVGCloudyIcon v-if="item.weather[0].main == 'Clouds'" />
        <SVGRainIcon v-if="item.weather[0].main == 'Rain'" />
        <SVGClearIcon v-if="item.weather[0].main == 'Clear'" />
        <SVGSnowIcon v-if="item.weather[0].main == 'Snow'" />
      </li>
    </ul>
  </div>
</template>
