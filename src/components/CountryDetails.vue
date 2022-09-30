<template>
  <div v-if="countryInfo">
    <img
      :src="`https://flagcdn.com/w320/${countryInfo.alpha2Code.toLowerCase()}.png`"
      alt=""
      class="mb-4"
    />
    <h2>{{ countryInfo.name.common }}</h2>
    <ul class="list-group list-group-flush">
      <li
        class="list-group-item d-flex justify-content-between align-items-center"
      >
        <p class="fw-bold">Capital</p>

        <p v-if="countryInfo.capital.length === 0">
          This country does not have a capital
        </p>
        <p v-else>{{ countryInfo.capital[0] }}</p>
      </li>
      <li
        class="list-group-item d-flex justify-content-between align-items-center"
      >
        <p class="fw-bold">Area</p>
        <p>{{ countryInfo.area }}.km<sup>2</sup></p>
      </li>
      <li class="list-group-item d-flex justify-content-between flex-column">
        <p class="fw-bold">Borders:</p>
        <p v-if="countryInfo.borders.length === 0">
          This country has no borders! <br />
          How about you invade and make some of your own!
        </p>
        <RouterLink
          v-else
          :to="`/country/${border}`"
          v-for="(border, index) in countryInfo.borders"
          :key="index"
        >
          {{ border }}
        </RouterLink>
      </li>
    </ul>
  </div>
</template>

<script setup>
import { watch, ref, onMounted, computed } from "vue";
import { useRoute } from "vue-router";

// data property to dynamically check if we have any data from the api and provide visbility onto the UI :)
const countryInfo = ref(null);

// data ref that will be used to store info from the to API
const name = ref();
const capital = ref();
const area = ref();
const borders = ref();

// routing ref - data ref that will be used to store info from the to API
const alpha3Code = ref();
// image ref  - data ref that will be used to store info from the to API
const alpha2Code = ref();

// Constant to use the route from the vue-router inside of this specific SFC - SIngle fIle COmponent
const route = useRoute();

// Function that will handle the fetching of info from api in order to provide visibolity of each country. - BIGGEST/HEAVY/HIERACHICAL FUNCTION INSIDE THIS FILE
const getCountryByAlphaCode = async () => {
  // variable to get the current route from the app
  const alpha3Code = route.params.alpha3Code;

  // variable to call async the api endpoint pointing to the specific country via its alpha3code
  const response = await fetch(
    `https://ih-countries-api.herokuapp.com/countries/${alpha3Code}`
  );

  // variable to store info from api and cleanUp it's JSON format with json() method.
  const finalResponse = await response.json();

  // Asignar los valores correspondientes de la info del API a las variables reactivas que nos hemos definido arriba :)
  const name = finalResponse.name.common;
  const capital = finalResponse.capital[0];
  const area = finalResponse.area;
  const borders = finalResponse.borders;
  const alpha2Code = finalResponse.alpha2Code;

  countryInfo.value = finalResponse;

  return { name, capital, borders, area, alpha2Code, countryInfo };
};

//Usamos onMounted() hook para ejecutar la funcion getCountryByAlphaCode() antes de que se renderize el componente como tal para obtener la informacion de la api y poder plasmarla dentro de la UI
onMounted(() => {
  getCountryByAlphaCode();
});

// Usar un computed function para tener visbilidad o basicamente tener accesso a la ruta que nos da el programa para ejecutar en 1ra instancia el famoso alpha3Code
const countryCode = computed(() => {
  return route.params.alpha3Code;
});

//REMEMBER TO WRITE DEFINITION HERE
watch(countryCode, (newCountryCode) => {
  getCountryByAlphaCode();
});

//onMounted Hook
</script>

<style></style>
