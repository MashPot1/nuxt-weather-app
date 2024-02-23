<script setup>
import axios from "axios";

const weatherData = ref({});
const forecastData = ref({});
const input = ref("London");
const isFunctionRunning = ref(false);
const errorMessage = ref("");

const fetchData = async (city) => {
  if (isFunctionRunning.value) return;

  isFunctionRunning.value = true;

  try {
    const { data } = await axios.get(
      `http://api.openweathermap.org/data/2.5/weather?q=${city}&APPID=1ca2a8e2dece60f8d305661e6e7c47c9&units=metric&cnt=24`
    );
    const { main, name, weather, wind } = data;
    weatherData.value = { main, name, weather, wind };
    errorMessage.value = "";
  } catch (error) {
    errorMessage.value = error.message;
    console.error("Error fetching weather data", error);
  }

  try {
    const { data } = await axios.get(
      `http://api.openweathermap.org/data/2.5/forecast?q=${city}&APPID=1ca2a8e2dece60f8d305661e6e7c47c9&units=metric&cnt=24`
    );
    forecastData.value = data.list;
    errorMessage.value = "";
  } catch (error) {
    errorMessage.value = error.message;
    console.error("Error fetching forecast data", error);
  }

  console.log(forecastData);
  setTimeout(() => {
    isFunctionRunning.value = false;
  }, 1000);
};

const handleFetchData = async () => {
  await fetchData(input.value);
};

onMounted(async () => {
  await fetchData(input.value);
});
</script>

<template>
  <div class="content">
    <div class="search">
      <input v-model="input" type="text" @keypress.enter="handleFetchData()" />
      <button @click="handleFetchData">Search</button>
    </div>

    <div v-if="errorMessage" class="error">
      <p>This city doesn't exist</p>
    </div>

    <div v-if="Object.keys(weatherData).length == 0" class="loader"></div>

    <WeatherComponent :weatherData="weatherData" />

    <ForecastComponent :forecastData="forecastData" />
  </div>
</template>

<style lang="scss">
$white05: rgba(255, 255, 255, 0.5);
$white08: rgba(255, 255, 255, 0.8);
$black08: rgba(0, 0, 0, 0.8);

$border-white06: rgba(255, 255, 255, 0.6);

@font-face {
  font-family: "Overpass Regular";
  src: url("./Overpass-Regular.ttf") format("truetype");
}

@font-face {
  font-family: "Overpass Semibold";
  src: url("./Overpass-Semibold.ttf") format("truetype");
}

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: "Overpass Regular";
}

.content {
  width: 100%;
  height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  background: linear-gradient(
    90deg,
    rgba(58, 136, 180, 0.4009978991596639) 0%,
    rgba(253, 29, 109, 0.4009978991596639) 25%,
    rgba(253, 62, 38, 0.604359243697479) 50%,
    rgba(252, 126, 55, 0.7987570028011204) 75%,
    rgba(252, 176, 69, 0.8491771708683473) 100%
  );
}

.loader {
  margin-top: 320px;
  border: 8px solid rgba(0, 0, 0, 0.1);
  border-left-color: $white05;
  border-radius: 50%;
  width: 160px;
  height: 160px;
  animation: spin 1s linear infinite;

  @keyframes spin {
    0% {
      transform: rotate(0deg);
    }
    100% {
      transform: rotate(360deg);
    }
  }
}

.search {
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: center;
  gap: 20px;
  margin-bottom: 20px;
}

input {
  font-size: 2em;
  color: $black08;
  padding: 15px;
  background: $white05;
  border: $white08 1px solid;
  border-radius: 25px;
  &:focus {
    outline-offset: 2px;
    outline-color: $white05;
    outline-width: 1px;
  }
}

button {
  font-size: 1.5em;
  color: $black08;
  padding: 20px;
  background: $white05;
  border: rgba(255, 255, 255, 0.8) 1px solid;
  border-radius: 25px;
  &:hover {
    cursor: pointer;
  }
}

.weatherCard {
  font-size: 2.5em;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 20px;
  background: $white05;
  border: $white08 1px solid;
  border-radius: 25px;
}

.forecastCardList {
  display: grid;
  grid-template-rows: auto auto auto;
  grid-template-columns: repeat(8, 1fr);
  gap: 10px;
}

.forecastCard {
  @extend .weatherCard;
  font-size: 1em;
  padding: 10px;
}
</style>
