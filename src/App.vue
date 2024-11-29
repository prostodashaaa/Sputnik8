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
            v-if="searchExcursion"
            class="img-excursion"
            src="./components/icons/Close.svg"
            alt="Сбросить"
            @click="reset"
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
              @click.stop="selectCity(city)"
            >
              {{ city.name }}
            </li>
          </ul>
        </div>
      </div>
      <div class="main__cards" v-if="filteredExcursion.length > 0">
        <div
          class="cards_item"
          v-for="excursion in filteredExcursion"
          :key="excursion.title"
        >
          <div class="cards_item_container-image">
            <img
              class="cards_item-image"
              :src="excursion.image_big"
              alt="Фото"
            />
          </div>
          <div class="cards_item-description">
            <div class="description__raiting">
              <img src="./components/icons/star.svg" alt="Оценка" />
              <p class="avg_raiting">
                {{ excursion.customers_review_rating }}
              </p>
              <p class="count_raiting">({{ excursion.reviews }})</p>
            </div>
            <h3 class="description__title">{{ excursion.title }}</h3>
          </div>
          <div class="description__price">
            <p class="description__price_min">от {{ excursion.price }}</p>
            <p class="description__price_categories">за экскурсию</p>
          </div>
        </div>
      </div>
      <div class="main__no-cards" v-else>
        <p class="main__no-cards_p">Поиск не дал результатов</p>
        <button class="main__no-cards_button" @click="resetAll">
          Сбросить фильтры
        </button>
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
  city_id: number;
}

export default {
  name: "App",
  data() {
    return {
      cities: [] as City[],
      excursions: [] as Excursion[],
      filteredCities: [] as City[],
      filteredExcursion: [] as Excursion[],
      searchCity: "" as string,
      searchExcursion: "" as string,
      showCityList: false as boolean,
      showExcursionList: false as boolean,
    };
  },

  methods: {
    async loadCities(): Promise<void> {
      try {
        const response = await axios.get(
          "/api/v1/cities?api_key=873fa71c061b0c36d9ad7e47ec3635d9&limit=5&page=1&username=frontend@sputnik8.com"
        );
        this.cities = response.data;
        this.filteredCities = this.cities
          .sort(() => Math.random() - 0.5)
          .slice(0, 5);
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
      this.filteredCities = this.cities
        .filter((city) =>
          city.name.toLowerCase().includes(this.searchCity.toLowerCase())
        )
        .slice(0, 5);
    },

    filterExcursion(): void {
      this.filteredExcursion = this.excursions.filter((excursion) => {
        const matchesTitle = this.searchExcursion
          ? excursion.title
              .toLowerCase()
              .includes(this.searchExcursion.toLowerCase())
          : true;

        const matchesCity = this.searchCity
          ? excursion.city_id ===
            this.cities.find((city) => city.name === this.searchCity)?.id
          : true;

        return matchesTitle && matchesCity;
      });
    },

    selectCity(city: City): void {
      this.searchCity = city.name;
      this.showCityList = false;
      this.filterExcursion();
    },

    reset(): void {
      this.searchExcursion = "";
    },

    resetAll(): void {
      this.searchExcursion = "";
      this.searchCity = "";
      this.filteredExcursion = this.excursions;
    },
  },

  mounted(): void {
    this.loadCities();
    this.loadExursion();
  },
};
</script>

<style src="./assets/main.css"></style>
