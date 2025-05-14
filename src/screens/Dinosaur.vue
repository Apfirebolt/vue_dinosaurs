<template>
  <div class="min-h-screen bg-info flex items-center justify-center">
    <div class="bg-white shadow-lg rounded-lg p-8">
      <h1 class="text-4xl font-bold text-primary text-center mb-4">Dinosaurs</h1>
      <p class="text-gray-700 leading-relaxed mb-4">
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

      <ag-grid-vue
        v-if="dinosaurData"
        class="ag-theme-alpine"
        style="width: 100%; height: 400px"
        :columnDefs="columnDefs"
        :rowData="dinosaurData.results"
        :modules="modules"
        :defaultColDef="defaultColDef"
        :gridOptions="gridOptions"
      />

      <div class="flex justify-between mt-4">
        <button
          @click="goToPreviousPage"
          :disabled="!dinosaurData || !dinosaurData.previous"
          class="bg-primary text-white px-4 py-2 rounded hover:bg-blue-600"
        >
          Previous
        </button>
        <button
          @click="goToNextPage"
          :disabled="!dinosaurData || !dinosaurData.next"
          class="bg-primary text-white px-4 py-2 rounded hover:bg-blue-600"
        >
          Next
        </button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { onMounted, ref } from "vue";
import axios from "axios";
import "@ag-grid-community/styles/ag-grid.css";
import "@ag-grid-community/styles/ag-theme-alpine.css";
import { ClientSideRowModelModule } from "@ag-grid-community/client-side-row-model";
import { AgGridVue } from "@ag-grid-community/vue3";
import Loader from "../components/Loader.vue";

const dinosaurData = ref(null);
const loading = ref(false);
const currentPage = ref(1);
const modules = ref([ClientSideRowModelModule]);

// ag grid config
const columnDefs = ref([
  { field: "id", headerName: "ID", sortable: true, filter: true },
  { field: "name", headerName: "Name", sortable: true, filter: true },
  { field: "diet", headerName: "Diet", sortable: true, filter: true },
  { field: "period", headerName: "Period", sortable: true, filter: true },
  { field: "lived_in", headerName: "Lived In", sortable: true, filter: true },
  { field: "type", headerName: "Type", sortable: true, filter: true },
  { field: "length", headerName: "Length", sortable: true, filter: true },
  { field: "taxonomy", headerName: "Taxonomy", sortable: true, filter: true },
  { field: "named_by", headerName: "Named By", sortable: true, filter: true },
  { field: "species", headerName: "Species", sortable: true, filter: true },
]);

const defaultColDef = ref({
  flex: 1,
  minWidth: 100,
  resizable: true,
});

const gridOptions = ref({
  animateRows: true,
  rowHeight: 50,
  headerHeight: 50,
});
const getDinosaurData = async () => {
  try {
    loading.value = true;
    const response = await axios.get(
      `https://softgenie.org/api/dinosaur?page=${currentPage.value}`
    );
    dinosaurData.value = response.data;
  } catch (error) {
    console.error("Error fetching dinosaur data:", error);
  } finally {
    loading.value = false;
  }
};

const goToNextPage = () => {
  if (dinosaurData.value && dinosaurData.value.next) {
    currentPage.value++;
    getDinosaurData();
  }
};

const goToPreviousPage = () => {
  if (dinosaurData.value && dinosaurData.value.previous) {
    currentPage.value--;
    getDinosaurData();
  }
};

onMounted(() => {
  getDinosaurData();
});
</script>
