<script setup lang="ts">
import { computed, defineProps } from "vue";

interface WeatherItem {
  main: string;
  description: string;
}

interface WeatherData {
  name: string;
  main: { temp: number };
  weather: WeatherItem[];
  wind: { speed: number };
  clouds: { all: number };
}

const props = defineProps<{
  weatherData: WeatherData | null;
}>();

const formattedDate = computed(() =>
  new Date().toLocaleDateString("pt-BR", {
    weekday: "long",
    day: "2-digit",
    month: "long",
  })
);

const weatherIcon = computed(() => {
  const iconMap: Record<string, string> = {
    Clear: "☀️",
    Clouds: "☁️",
    Rain: "🌧️",
    Snow: "❄️",
    Thunderstorm: "⛈️",
    Drizzle: "🌦️",
    Mist: "🌫️",
  };

  const mainWeather = props.weatherData?.weather?.[0]?.main;
  return mainWeather ? iconMap[mainWeather] || "🌤️" : "🌤️";
});
</script>

<template>
  <div class="flex items-center justify-center">
    <div
      v-if="props.weatherData"
      class="bg-white/20 backdrop-blur-lg rounded-xl shadow-lg p-6 w-80 text-white"
    >
      <div class="flex items-center gap-2 mb-2">
        <span class="text-xl">📍</span>
        <h2 class="text-lg font-semibold">{{ props.weatherData.name }}</h2>
      </div>

      <p class="text-sm opacity-90 mb-4">{{ formattedDate }}</p>

      <div class="flex items-center justify-between">
        <div class="text-6xl">
          <span>{{ weatherIcon }}</span>
        </div>
        <div class="text-right">
          <p class="text-5xl font-bold">
            {{ Math.round(props.weatherData.main.temp) }}°C
          </p>
          <p class="text-sm opacity-80 capitalize">
            {{ props.weatherData.weather[0].description }}
          </p>
        </div>
      </div>

      <div class="mt-4 text-sm opacity-90">
        <p>☁️ Nuvens: {{ props.weatherData.clouds.all }}%</p>
        <p>💨 Vento: {{ props.weatherData.wind.speed }} m/s</p>
      </div>
    </div>

    <div v-else class="text-white text-center">
      <p class="text-2xl animate-pulse">Carregando dados do clima...</p>
    </div>
  </div>
</template>
