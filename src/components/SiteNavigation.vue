<template>
  <header class="sticky top-0 bg-weather-primary shadow-lg">
    <nav class="container max-w-3xl flex flex-col sm:flex-row items-center gap-4 text-white py-6 ">
      <RouterLink :to="{ name: 'home' }">
        <div class="flex items-center gap-3 flex-1">
          <i class="fa-solid fa-sun text-2xl"></i>
          <p class="text-2xl">The Local Weather</p>
        </div>
      </RouterLink>
      <div class="flex gap-3 flex-1 justify-end">
        <i @click="toggleModal"
          class="fa-solid fa-circle-info text-xl hover:text-weather-secondary duration-150 cursor-pointer"></i>
        <i class="fa-solid fa-plus text-xl hover:text-weather-secondary duration-200 cursor-pointer" @click="addCity"
          v-if="route.query.preview"></i>
      </div>
      <base-modal :modal-active="modalActive" @close-modal="toggleModal">
        <h1 class="text-black">Hello Modal.</h1>
        <p class="text-black">
          Lorem ipsum dolor sit amet, consectetur adipiscing elit. Suspendisse nec porttitor dolor. Sed posuere
          porttitor suscipit. Duis sed nulla ac ipsum scelerisque placerat. Quisque dapibus viverra lorem. Aliquam id
          nunc magna. Duis porttitor efficitur urna ut pulvinar. Quisque vehicula, libero sed lobortis maximus, libero
          nunc gravida tortor, nec facilisis felis sem id felis. Pellentesque convallis nec felis at gravida. Phasellus
          neque est, volutpat sit amet tortor sed, dapibus pharetra felis.

          Proin tristique ultrices malesuada. Donec scelerisque malesuada tellus, quis porttitor dui tempus quis. Nunc
          nunc justo, imperdiet in nunc at, tristique finibus ipsum. Etiam sodales ultricies magna, id auctor eros
          aliquet quis. Praesent vitae mauris non leo euismod ultricies. Quisque nibh nibh, porttitor nec gravida
          accumsan, fringilla at tortor. Sed vulputate convallis ipsum et rhoncus. Suspendisse eu varius nisi. Curabitur
          quis urna ut ex interdum mollis sit amet vel sapien. Mauris vulputate imperdiet augue, sit amet rutrum mauris
          auctor auctor. Suspendisse id risus mi. Pellentesque id pharetra dolor, vel pellentesque massa. Vestibulum at
          elit diam. Fusce sed diam velit.

          Donec facilisis sed tortor in rhoncus. Suspendisse placerat malesuada luctus. Pellentesque euismod felis nec
          lacus hendrerit, vitae auctor metus cursus. Vestibulum ante ipsum primis in faucibus orci luctus et ultrices
          posuere cubilia curae; Suspendisse bibendum fringilla luctus. Duis quis vestibulum massa, a feugiat orci.
          Proin ornare ante ut neque porttitor, id imperdiet erat vehicula. Mauris convallis laoreet libero sit amet
          tristique. Sed feugiat est neque, in auctor elit dapibus ut. Suspendisse sit amet interdum justo.
        </p>
      </base-modal>
    </nav>
  </header>
</template>

<script setup>
import { ref } from "@vue/reactivity";
import { RouterLink, useRoute, useRouter } from "vue-router";
import BaseModal from "./BaseModal.vue";
import { uid } from "uid";

const savedCities = ref([]);
const route = useRoute();
const router = useRouter();
const addCity = () => {
  if (localStorage.getItem("savedCities")) {
    savedCities.value = JSON.parse(
      localStorage.getItem("savedCities")
    )
  }

  const locationObj = {
    id: uid(),
    state: route.params.state,
    city: route.params.city,
    coords: {
      lat: route.query.lat,
      lon: route.query.lon,
    }
  }

  savedCities.value.push(locationObj);
  localStorage.setItem('savedCities', JSON.stringify(savedCities.value))

  let query = Object.assign({}, route.query);
  delete query.preview;
  query.id = locationObj.id
  router.replace({ query })
}

const modalActive = ref(null);
const toggleModal = () => {
  modalActive.value = !modalActive.value;
};
</script>

<style>

</style>