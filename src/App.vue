<template>
  <div id="app">
    <header class="header">
      <div class="header__logo">
        <img src="./components/icons/logo.svg" alt="Логотип" />
      </div>
      <h1 class="header__h1">Экскурсии по всему миру</h1>
    </header>
    <main class="main">
      <div class="main__form">
        <div class="form_input-excursion">
          <input
            type="text"
            v-model="searchExcursion"
            @input="filterExcursion"
            class="input-excursion"
            placeholder="Введите название экскурсии"
          />
          <img
            class="img-excursion"
            src="./components/icons/Close.svg"
            alt="Сбросить"
          />
        </div>
        <div class="form_input-city" @click="showCityList = !showCityList">
          <input
            type="text"
            class="input-city"
            v-model="searchCity"
            @input="filterCities"
            placeholder="Выбрать город"
          />
          <img
            class="img-city"
            src="./components/icons/Arrow_Down.svg"
            alt="Развернуть"
          />
          <ul class="list-city" v-if="showCityList">
            <li
              class="list-city_item"
              v-for="city in filteredCities"
              @click="selectCity(city)"
            >
              {{ city.name }}
            </li>
          </ul>
        </div>
      </div>
      <div class="main__cards" v-if="showExcursionList">
        <div class="cards_item">
          <img
            class="cards_item-image"
            :src="excursions.image_big"
            alt="Фото"
          />
          <div class="cards_item-description">
            <div class="description__raiting">
              <img src="./components/icons/star.svg" alt="Оценка" />
              <p class="avg_raiting">
                {{ excursions.customers_review_rating }}
              </p>
              <p class="count_raiting">{{ excursions.reviews }}</p>
            </div>
            <h3 class="description__title">{{ excursions.title }}</h3>
            <div class = 'description__price'>
              <p class = 'description__price_min'>от {{ excursions.price }} </p>
              <p class = 'description__price_categories'>за экскурсию</p>
            </div>
          </div>
        </div>
      </div>
    </main>
  </div>
</template>

<script lang="ts">
import axios from "axios";

interface City {
  id: number;
  name: string;
}

interface Excursion {
  image_big: string;
  customers_review_rating: number;
  reviews: number;
  title: string;
  price: number;
}

export default {
  name: "App",
  data() {
    return {
      cities: [] as City[],
      excursions: [] as Excursion[],
      filteredCities: [] as City,
      filteredExcursion: [] as Excursion,
      searchCity: "" as string,
      searchExcursion: "" as string,
      showCityList: false as boolean,
      showExcursionList: true as boolean,
    };
  },

  methods: {
    async loadCities(): Promise<void> {
      try {
        const response = await axios.get(
          "/api/v1/cities?api_key=873fa71c061b0c36d9ad7e47ec3635d9&limit=5&page=1&username=frontend@sputnik8.com"
        );
        this.cities = response.data;
        this.filteredCities = this.cities;
      } catch (error) {
        console.error("Ошибка загрузки городов:", error);
      }
    },

    async loadExursion(): Promise<void> {
      try {
        const response = await axios.get(
          "/api/v1/products?api_key=873fa71c061b0c36d9ad7e47ec3635d9&username=frontend@sputnik8.com"
        );
        this.excursions = response.data;
        this.filteredExcursion = this.excursions;
      } catch (error) {
        console.error("Ошибка загрузки экскурсий:", error);
      }
    },

    filterCities(): void {
      this.filteredCities = this.cities.filter((city) =>
        city.name.toLowerCase().includes(this.searchCity.toLowerCase())
      );
    },

    filterExcursion(): void {
      this.filteredExcursion = this.excursions.filter((excursion) =>
        excursion.title
          .toLowerCase()
          .includes(this.searchExcursion.toLowerCase())
      );
    },

    selectCity(city: City): void {
      this.searchCity = city.name;
      this.showCityList = false;
    },
  },

  mounted(): void {
    this.loadCities();
    this.loadExursion();
  },
};
</script>

<style src="./assets/main.css"></style>
