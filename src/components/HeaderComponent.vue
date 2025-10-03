<template>
  <Disclosure
    :class="['fixed top-0 z-20 w-full py-3 transition-all duration-300', isScrolledDown ? 'bg-dark dark:bg-slate-800' : 'bg-secondary dark:bg-slate-700 dark:text-white']"
    as="nav" v-slot="{ open }">
    <div class="w-4/5 mx-auto">
      <div class="flex items-center h-16">
        <div class="flex items-center w-full justify-between">
          <h2 class="text-2xl text-white font-bold">Vue Dinosaurs</h2>
          <div class="hidden w-1/5 sm:block sm:ml-6">
            <div class="flex justify-between items-center space-x-4 w-full">
              <router-link v-for="link in links" :key="link.name" :to="link.href"
                class="menu-link text-white px-3 py-2 rounded-md font-medium text-lg relative">
                {{ link.name }}
              </router-link>
            </div>
          </div>
        </div>

        <div class="-mr-2 flex sm:hidden">
          <DisclosureButton
            class="inline-flex items-center justify-center p-2 rounded-md text-info hover:text-white hover:bg-primary transition-all duration-200 focus:outline-none focus:ring-2 focus:ring-inset focus:ring-white">
            <span class="sr-only">Open main menu</span>
            <MenuIcon v-if="!open" class="block h-6 w-6" aria-hidden="true" />
            <XIcon v-else class="block h-6 w-6" aria-hidden="true" />
          </DisclosureButton>
        </div>
      </div>
    </div>

    <DisclosurePanel class="sm:hidden">
      <div class="px-2 pt-2 pb-3 space-y-1">
        <router-link v-for="link in links" :key="link.name" :to="link.href"
          class="menu-link-mobile text-gray-300 block px-3 py-2 rounded-md text-base font-medium relative">
          {{ link.name }}
        </router-link>
      </div>
    </DisclosurePanel>
  </Disclosure>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import { Disclosure, DisclosureButton, DisclosurePanel } from "@headlessui/vue";
import { MenuIcon, XIcon } from "@heroicons/vue/outline";

const isScrolledDown = ref(false);
const links = [
  { name: 'Home', href: '/' },
  { name: 'About', href: '/about' },
  { name: 'Dinosaurs', href: '/dinosaur' },
];

const checkScroll = () => {
  isScrolledDown.value = window.scrollY > 100;
};

window.addEventListener('scroll', checkScroll);

onMounted(() => {
  checkScroll();
});
</script>

<style scoped>
.menu-link {
  position: relative;
  transition: color 0.2s;
}

.menu-link::after {
  content: '';
  position: absolute;
  left: 0;
  bottom: 4px;
  width: 100%;
  height: 2px;
  background: linear-gradient(90deg, #38bdf8 0%, #6366f1 100%);
  transform: scaleX(0);
  transition: transform 0.3s cubic-bezier(.4, 0, .2, 1);
  border-radius: 1px;
}

.menu-link:hover::after,
.menu-link:focus::after {
  transform: scaleX(1);
}

.menu-link-mobile {
  position: relative;
  transition: color 0.2s;
}

.menu-link-mobile::after {
  content: '';
  position: absolute;
  left: 0;
  bottom: 4px;
  width: 100%;
  height: 2px;
  background: linear-gradient(90deg, #38bdf8 0%, #6366f1 100%);
  transform: scaleX(0);
  transition: transform 0.3s cubic-bezier(.4, 0, .2, 1);
  border-radius: 1px;
}

.menu-link-mobile:hover::after,
.menu-link-mobile:focus::after {
  transform: scaleX(1);
}
</style>