<script setup lang="ts">
import { ref } from "vue";
import axios from "axios";
import WeatherCard from "./components/WeatherCard.vue";
import AppHeader from "./components/AppHeader.vue";
import logo from "@/assets/images/Clima-logo1.png";

// Interfaces
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

interface CitySuggestion {
  name: string;
  country: string;
}

const city = ref("");
const weather = ref<WeatherData | null>(null);
const suggestions = ref<CitySuggestion[]>([]);
const apiKey = "c2c858b3e4c81a405782b4a93c72bf83";

// Buscar clima
const fetchWeather = async (cityName: string) => {
  if (!cityName) return;
  try {
    const url = `https://api.openweathermap.org/data/2.5/weather?q=${cityName}&appid=${apiKey}&units=metric&lang=pt_br`;
    const response = await axios.get<WeatherData>(url);
    weather.value = response.data;
  } catch (error) {
    console.error("Erro ao buscar clima:", error);
    weather.value = null;
  }
};

// Buscar cidades com filtro de duplicados
const searchCities = async () => {
  if (city.value.length < 2) {
    suggestions.value = [];
    return;
  }

  try {
    const url = `https://api.openweathermap.org/geo/1.0/direct?q=${city.value}&limit=5&appid=${apiKey}`;
    const response = await axios.get(url);

    // Filtrar duplicados
    const unique = new Map<string, CitySuggestion>();
    response.data.forEach((c: CitySuggestion) => {
      const key = `${c.name},${c.country}`;
      if (!unique.has(key)) unique.set(key, c);
    });

    suggestions.value = Array.from(unique.values());
  } catch (error) {
    console.error("Erro ao buscar cidades:", error);
    suggestions.value = [];
  }
};

const selectCity = (cityObj: CitySuggestion) => {
  city.value = `${cityObj.name}, ${cityObj.country}`;
  suggestions.value = [];
  fetchWeather(cityObj.name);
  (document.activeElement as HTMLElement)?.blur();
};
</script>

<template>
  <div
    class="flex flex-col items-center justify-center min-h-screen bg-gradient-to-br from-blue-400 to-indigo-600 text-white p-4"
  >
    <!-- Header fixo -->
    <AppHeader v-if="weather" class="fixed top-0 left-0 w-full z-20" />

    <!-- Logo -->
    <img
      v-if="!weather"
      :src="logo"
      alt="Clima ícone"
      class="w-20 h-20 mb-4 drop-shadow-lg"
    />

    <!-- Campo de busca -->
    <div
      class="w-full max-w-md relative"
      :class="weather ? 'mt-28 mb-6' : 'mb-6 mt-20'"
    >
      <div class="flex gap-2 relative">
        <span class="absolute inset-y-0 left-3 flex items-center text-gray-400"
          >🔍</span
        >

        <input
          v-model="city"
          @input="searchCities"
          @keyup.enter="fetchWeather(city)"
          type="text"
          placeholder="Digite o nome da cidade..."
          class="flex-1 pl-10 pr-4 py-2 rounded-lg text-black focus:outline-none focus:ring-2 focus:ring-blue-300"
        />
        <button
          @click="fetchWeather(city)"
          class="px-4 py-2 bg-blue-400 rounded-lg hover:bg-blue-700 transition"
        >
          Buscar
        </button>
      </div>

      <!-- Sugestões -->
      <ul
        v-if="suggestions.length > 0"
        class="absolute bg-white text-black mt-1 w-full rounded-lg shadow-lg max-h-60 overflow-y-auto z-10"
      >
        <li
          v-for="(s, index) in suggestions"
          :key="index"
          @click="selectCity(s)"
          class="px-4 py-2 cursor-pointer hover:bg-gray-200"
        >
          {{ s.name }}, {{ s.country }}
        </li>
      </ul>
    </div>

    <!-- Card do clima -->
    <WeatherCard v-if="weather" :weatherData="weather" />
  </div>
</template>
