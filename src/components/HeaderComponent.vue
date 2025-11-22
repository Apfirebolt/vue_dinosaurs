<template>
  <Disclosure
    :class="['fixed top-0 z-20 w-full py-3 transition-all duration-300', isScrolledDown ? 'bg-dark dark:bg-slate-800' : 'bg-secondary dark:bg-slate-700 dark:text-white']"
    as="nav" v-slot="{ open }">
    <div class="w-4/5 mx-auto">
      <div class="flex items-center h-16">
        <div class="flex items-center w-full justify-between">
          <h2 class="text-2xl text-white font-bold">Vue Dinosaurs</h2>
          <div class="hidden sm:block sm:ml-6">
            <div class="flex justify-between items-center space-x-4 w-full">
              <router-link v-for="link in links" :key="link.name" :to="link.href"
                class="menu-link text-white px-3 py-2 rounded-md font-medium text-lg relative">
                {{ link.name }}
              </router-link>
              <!-- Mega Dropdown Trigger -->
              <div class="relative"
                   @mouseenter="showMegaMenu = true"
                   @mouseleave="showMegaMenu = false">
                <button class="menu-link text-white px-3 py-2 rounded-md font-medium text-lg relative">
                  Resources
                </button>
                <!-- Mega Dropdown Menu -->
                <div v-if="showMegaMenu"
                     class="absolute left-0 top-full w-screen bg-white dark:bg-slate-800 shadow-lg py-8 px-16 z-30 flex justify-center"
                     @mouseenter="showMegaMenu = true"
                     @mouseleave="showMegaMenu = false">
                  <div class="grid grid-cols-3 gap-8 w-4/5">
                    <div>
                      <h3 class="font-bold mb-2 text-gray-700 dark:text-white">Documentation</h3>
                      <ul>
                        <li><router-link to="/docs/getting-started" class="block py-1 text-gray-600 dark:text-gray-300 hover:underline">Getting Started</router-link></li>
                        <li><router-link to="/docs/api" class="block py-1 text-gray-600 dark:text-gray-300 hover:underline">API Reference</router-link></li>
                      </ul>
                    </div>
                    <div>
                      <h3 class="font-bold mb-2 text-gray-700 dark:text-white">Community</h3>
                      <ul>
                        <li><a href="https://forum.vuejs.org" target="_blank" class="block py-1 text-gray-600 dark:text-gray-300 hover:underline">Forum</a></li>
                        <li><a href="https://chat.vuejs.org" target="_blank" class="block py-1 text-gray-600 dark:text-gray-300 hover:underline">Chat</a></li>
                      </ul>
                    </div>
                    <div>
                      <h3 class="font-bold mb-2 text-gray-700 dark:text-white">Tools</h3>
                      <ul>
                        <li><a href="https://vitejs.dev" target="_blank" class="block py-1 text-gray-600 dark:text-gray-300 hover:underline">Vite</a></li>
                        <li><a href="https://pinia.vuejs.org" target="_blank" class="block py-1 text-gray-600 dark:text-gray-300 hover:underline">Pinia</a></li>
                      </ul>
                    </div>
                  </div>
                </div>
              </div>
              <!-- End Mega Dropdown -->
            </div>
          </div>
        </div>
        <!-- ...rest unchanged... -->
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
    <!-- ...rest unchanged... -->
    <DisclosurePanel class="sm:hidden">
      <div class="px-2 pt-2 pb-3 space-y-1">
        <router-link v-for="link in links" :key="link.name" :to="link.href"
          class="menu-link-mobile text-gray-300 block px-3 py-2 rounded-md text-base font-medium relative">
          {{ link.name }}
        </router-link>
        <!-- Add Resources to mobile menu if needed -->
        <div>
          <span class="menu-link-mobile text-gray-300 block px-3 py-2 rounded-md text-base font-medium relative">Resources</span>
          <div class="pl-4">
            <router-link to="/docs/getting-started" class="block py-1 text-gray-400 hover:underline">Getting Started</router-link>
            <router-link to="/docs/api" class="block py-1 text-gray-400 hover:underline">API Reference</router-link>
            <a href="https://forum.vuejs.org" target="_blank" class="block py-1 text-gray-400 hover:underline">Forum</a>
            <a href="https://chat.vuejs.org" target="_blank" class="block py-1 text-gray-400 hover:underline">Chat</a>
            <a href="https://vitejs.dev" target="_blank" class="block py-1 text-gray-400 hover:underline">Vite</a>
            <a href="https://pinia.vuejs.org" target="_blank" class="block py-1 text-gray-400 hover:underline">Pinia</a>
          </div>
        </div>
      </div>
    </DisclosurePanel>
  </Disclosure>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import { Disclosure, DisclosureButton, DisclosurePanel } from "@headlessui/vue";
import { MenuIcon, XIcon } from "@heroicons/vue/outline";

const isScrolledDown = ref(false);
const showMegaMenu = ref(true);

const links = [
  { name: 'Home', href: '/' },
  { name: 'About', href: '/about' },
  { name: 'Dinosaurs', href: '/dinosaur' },
  { name: 'Composables', href: '/composables' }
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

/* Mega menu styles */
.mega-menu {
  min-width: 800px;
  max-width: 1200px;
  border-radius: 12px;
  box-shadow: 0 8px 32px rgba(0,0,0,0.12);
  border: 1px solid #e5e7eb;
  background: #fff;
  padding: 2rem 3rem;
  transition: box-shadow 0.2s;
  display: flex;
  gap: 2rem;
}

.dark .mega-menu {
  background: #1e293b;
  border-color: #334155;
  box-shadow: 0 8px 32px rgba(30,41,59,0.24);
}
</style>