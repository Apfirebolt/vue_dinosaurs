<template>
  <div class="min-h-screen bg-emerald-900 text-neutral-100 pt-28 pb-16 px-4 sm:px-6 lg:px-8">
    <div class="max-w-7xl mx-auto space-y-8">
      <div class="text-center max-w-3xl mx-auto space-y-3">
        <div class="inline-flex items-center gap-2 px-3 py-1 rounded-full bg-emerald-500/10 text-emerald-400 text-xs font-semibold uppercase tracking-wider border border-emerald-500/20">
          <span>🦖 Fossil Registry</span>
        </div>
        <h1 class="text-3xl sm:text-4xl font-extrabold tracking-tight text-white">
          Dinosaur Classification Database
        </h1>
        <p class="text-sm sm:text-base text-neutral-100">
          Explore complete taxonomic data, diet profiles, historical periods, and fossil excavation locales.
        </p>
      </div>

      <div class="flex flex-col sm:flex-row items-center justify-between gap-4">
        <div class="relative w-full sm:w-80">
          <span class="absolute inset-y-0 left-0 flex items-center pl-3.5 pointer-events-none text-neutral-500">
            <svg class="w-4 h-4" fill="none" viewBox="0 0 24 24" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z" />
            </svg>
          </span>
          <input
            v-model="searchText"
            type="text"
            placeholder="Quick search records..."
            class="w-full rounded-xl border border-neutral-800 bg-neutral-900/80 pl-10 pr-4 py-2 text-sm text-white placeholder-neutral-500 outline-none transition focus:border-emerald-500 focus:ring-1 focus:ring-emerald-500"
          />
        </div>

        <div class="flex items-center gap-2 w-full sm:w-auto justify-end">
          <button
            type="button"
            @click="clearFilters"
            class="rounded-lg border border-neutral-800 bg-neutral-900 px-3.5 py-2 text-xs font-medium text-neutral-300 transition hover:bg-neutral-800 hover:text-white"
          >
            Clear Filters
          </button>
        </div>
      </div>

      <div class="relative rounded-2xl border border-neutral-800 bg-neutral-900/40 p-4 shadow-2xl backdrop-blur-xl">
        <Loader v-if="loading" message="Loading fossil records..." :full-screen="false" />

        <div v-show="!loading" class="w-full h-[520px]">
          <ag-grid-vue
            class="ag-theme-alpine-dark custom-dino-grid w-full h-full"
            :columnDefs="columnDefs"
            :rowData="filteredRowData"
            :modules="modules"
            :defaultColDef="defaultColDef"
            :gridOptions="gridOptions"
            @grid-ready="onGridReady"
          />
        </div>

        <div class="mt-4 flex flex-col sm:flex-row items-center justify-between gap-4 border-t border-neutral-800/80 pt-4">
          <span class="text-xs text-neutral-100 font-mono">
            Page <strong class="text-white">{{ currentPage }}</strong>
            <span v-if="dinosaurData?.count"> • Total Records: {{ dinosaurData.count }}</span>
          </span>

          <div class="flex items-center gap-2">
            <button
              type="button"
              @click="goToPreviousPage"
              :disabled="!dinosaurData?.previous || loading"
              class="rounded-lg border border-neutral-800 bg-neutral-900 px-3.5 py-2 text-xs font-semibold text-neutral-300 transition hover:bg-neutral-800 hover:text-white disabled:opacity-40 disabled:cursor-not-allowed"
            >
              Previous
            </button>
            <button
              type="button"
              @click="goToNextPage"
              :disabled="!dinosaurData?.next || loading"
              class="rounded-lg border border-neutral-800 bg-neutral-900 px-3.5 py-2 text-xs font-semibold text-neutral-300 transition hover:bg-neutral-800 hover:text-white disabled:opacity-40 disabled:cursor-not-allowed"
            >
              Next
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from "vue";
import axios from "axios";
import "@ag-grid-community/styles/ag-grid.css";
import "@ag-grid-community/styles/ag-theme-alpine.css";
import { ClientSideRowModelModule } from "@ag-grid-community/client-side-row-model";
import { AgGridVue } from "@ag-grid-community/vue3";
import Loader from "../components/Loader.vue";

const dinosaurData = ref(null);
const rawResults = ref([]);
const loading = ref(false);
const currentPage = ref(1);
const searchText = ref("");
const gridApi = ref(null);

const modules = ref([ClientSideRowModelModule]);

const defaultColDef = ref({
  flex: 1,
  minWidth: 120,
  sortable: true,
  filter: true,
  resizable: true,
});

const columnDefs = ref([
  { field: "id", headerName: "ID", width: 70, flex: 0.5 },
  { 
    field: "name", 
    headerName: "Name", 
    cellClass: "font-semibold text-white",
    pinned: "left"
  },
  { 
    field: "diet", 
    headerName: "Diet",
    cellRenderer: (params) => {
      if (!params.value) return "—";
      const diet = params.value.toLowerCase();
      let badgeClass = "bg-neutral-800 text-neutral-400";
      if (diet.includes("carnivorous")) badgeClass = "bg-rose-500/10 text-rose-400 border border-rose-500/20";
      else if (diet.includes("herbivorous")) badgeClass = "bg-emerald-500/10 text-emerald-400 border border-emerald-500/20";
      else if (diet.includes("omnivorous")) badgeClass = "bg-sky-500/10 text-sky-400 border border-sky-500/20";

      return `<span class="inline-flex items-center px-2 py-0.5 rounded text-xs capitalize ${badgeClass}">${params.value}</span>`;
    }
  },
  { field: "period", headerName: "Period" },
  { field: "lived_in", headerName: "Lived In" },
  { field: "type", headerName: "Type" },
  { field: "length", headerName: "Length" },
  { field: "taxonomy", headerName: "Taxonomy", minWidth: 160 },
  { field: "named_by", headerName: "Named By", minWidth: 150 },
  { field: "species", headerName: "Species" },
]);

const gridOptions = ref({
  animateRows: true,
  rowHeight: 44,
  headerHeight: 44,
});

const onGridReady = (params) => {
  gridApi.value = params.api;
};

const getDinosaurData = async () => {
  try {
    loading.value = true;
    const response = await axios.get(
      `https://softgenie.org/api/dinosaur?page=${currentPage.value}`
    );
    dinosaurData.value = response.data;
    rawResults.value = response.data.results || [];
  } catch (error) {
    console.error("Error fetching dinosaur data:", error);
  } finally {
    loading.value = false;
  }
};

const filteredRowData = computed(() => {
  if (!searchText.value.trim()) {
    return rawResults.value;
  }
  const query = searchText.value.toLowerCase();
  return rawResults.value.filter(
    (dino) =>
      dino.name?.toLowerCase().includes(query) ||
      dino.diet?.toLowerCase().includes(query) ||
      dino.lived_in?.toLowerCase().includes(query) ||
      dino.period?.toLowerCase().includes(query)
  );
});

const clearFilters = () => {
  searchText.value = "";
  if (gridApi.value) {
    gridApi.value.setFilterModel(null);
    gridApi.value.setQuickFilter("");
  }
};

const goToNextPage = () => {
  if (dinosaurData.value?.next) {
    currentPage.value++;
    getDinosaurData();
  }
};

const goToPreviousPage = () => {
  if (dinosaurData.value?.previous && currentPage.value > 1) {
    currentPage.value--;
    getDinosaurData();
  }
};

onMounted(() => {
  getDinosaurData();
});
</script>

<style>
.custom-dino-grid {
  --ag-background-color: transparent;
  --ag-header-background-color: rgba(23, 23, 23, 0.7);
  --ag-border-color: rgba(64, 64, 64, 0.4);
  --ag-row-hover-color: rgba(38, 38, 38, 0.6);
  --ag-foreground-color: #d4d4d4;
  --ag-header-foreground-color: #ffffff;
  --ag-font-size: 13px;
  font-family: inherit;
  border-radius: 0.75rem;
  overflow: hidden;
}
</style>