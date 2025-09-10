<script setup lang="ts">
import climaLogo from "@/assets/images/Clima_logo2.png";
</script>

<template>
  <header
    class="w-full bg-sky-300/50 backdrop-blur-md py-2 shadow-md flex items-center justify-start gap-3"
  >
    <img :src="climaLogo" alt="clima ícone" class="w-16 h-16 ml-6" />

    <h1 class="text-xl font-semibold text-white drop-shadow">Clima App</h1>
  </header>
</template>
