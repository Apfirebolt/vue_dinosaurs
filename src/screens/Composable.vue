<template>
  <div class="min-h-screen bg-info flex items-center justify-center">
    <div class="bg-white shadow-lg rounded-lg p-4">
      <h1 class="text-4xl mt-16 font-bold text-primary text-center mb-4">
        Dinosaurs
      </h1>
      <p class="text-dark leading-relaxed mb-4">
        Welcome to the world of dinosaurs! These magnificent creatures roamed
        the Earth millions of years ago, and their legacy continues to fascinate
        us today. From the towering Brachiosaurus to the fierce Tyrannosaurus
        rex, dinosaurs come in all shapes and sizes.
      </p>
      <p class="text-gray-700 leading-relaxed mb-4">
        Explore the mysteries of their existence, learn about their habitats,
        and uncover the secrets of their extinction. Whether you're a
        paleontology enthusiast or just curious, there's always something new to
        discover about dinosaurs.
      </p>
      <p class="text-gray-700 leading-relaxed mb-5">
        Thank you for joining us on this prehistoric journey. Let's dive into
        the age of dinosaurs together!
      </p>

      <Loader v-if="loading" />

      <div class="grid grid-cols-3 gap-4 mb-2">
        <div
          v-for="dinosaur in dinosaurs"
          :key="dinosaur.Name"
          class="bg-gray-100 rounded-lg shadow p-4 flex flex-col items-center"
        >
          <h2 class="text-xl font-semibold mb-2 text-primary">{{ dinosaur.Name }}</h2>
          <p class="text-gray-500">{{ dinosaur.Description }}</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, watchEffect } from "vue";
import { useDinosaur } from "../composables/useDinosaur";

const { dinosaurs, getDinosaur } = useDinosaur();

watchEffect(() => {
  console.log('Changed value of dinosaurs:', dinosaurs.value);
});

onMounted(() => {
  if (dinosaurs.value.length === 0) {
    getDinosaur();
  }
});
</script>
