<script setup>
  import { ref, onMounted } from 'vue';
  import api from '@/plugins/axios';
  import { useGenreStore } from '@/stores/genre';
  import { useRouter } from 'vue-router'
const router = useRouter()

  import Loading from 'vue-loading-overlay';

  const isLoading = ref(false);


  const genreStore = useGenreStore();

  const formatDate = (date) => new Date(date).toLocaleDateString('pt-BR');

  onMounted(async () => {
  isLoading.value = true;
  await genreStore.getAllGenres('tv');
  isLoading.value = false;
});

  const programas = ref([]);

  const listTvs = async (genreId) => {
  genreStore.setCurrentGenreId(genreId);
  isLoading.value = true;
  const response = await api.get('discover/tv', {
    params: {
      with_genres: genreId,
      language: 'pt-BR',
    },
  });
  programas.value = response.data.results;
  isLoading.value = false;

  console.log(programas.value);
};

function openTv(tvId) {
  router.push({ name: 'TvDetails', params: { tvId } });
} 


</script>

<template>
  <h1>Programas de Tv</h1>
  <ul class="genre-list">
    <li
    v-for="genre in genreStore.genres"
    :key="genre.id"
    @click="listTvs(genre.id)"
    class="genre-item"
    :class="{ active: genre.id === genreStore.currentGenreId }"
  >

    {{ genre.name }}

  </li>
  </ul>

  <loading v-model:active="isLoading" is-full-page />

  <div class="tv-list">
  <div v-for="tv in programas" :key="tv.id" class="tv-card">
    <img
  :src="`https://image.tmdb.org/t/p/w500${tv.poster_path}`"
  :alt="tv.title"
  @click="openTv(tv.id)"
 />
 {{ tv.id }}
    <div class="tv-details">
      <p class="tv-tit le">{{ tv.name }}</p>
      <p class="tv-release-date">{{ formatDate(tv.first_air_date) }}</p>
      <p class="tv-genres">
        <span
  v-for="genre_id in tv.genre_ids"
  :key="genre_id"
  @click="listTvs(genre_id)"
  :class="{ active: genre_id === genreStore.currentGenreId }"
>
   {{ genreStore.getGenreName(genre_id) }}
</span>

      </p>
    </div>
  </div>
</div>


</template>
<style scoped>
.genre-list {
  display: flex;
  justify-content: center;
  flex-wrap: wrap;
  gap: 2rem;
  list-style: none;
  margin-bottom: 2rem;
}

.genre-item {
  background-color: #387250;
  border-radius: 1rem;
  padding: 0.5rem 1rem;
  color: #fff;
}

.genre-item:hover {
  cursor: pointer;
  background-color: #4e9e5f;
  box-shadow: 0 0 0.5rem #387250;
}
.tv-list {
  display: flex;
  flex-wrap: wrap;
  gap: 1rem;
}

.tv-card {
  width: 15rem;
  height: 30rem;
  border-radius: 0.5rem;
  overflow: hidden;
  box-shadow: 0 0 0.5rem #000;
}

.tv-card img {
  width: 100%;
  height: 20rem;
  border-radius: 0.5rem;
  box-shadow: 0 0 0.5rem #000;
}

.tv-detai {
  padding: 0 0.5rem;
}

.movie-title {
  font-size: 1.1rem;
  font-weight: bold;
  line-height: 1.3rem;
  height: 3.2rem;
}

.movie-genres {
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  align-items: flex-start;
  justify-content: center;
  gap: 0.2rem;
}

.movie-genres span {
  background-color: #748708;
  border-radius: 0.5rem;
  padding: 0.2rem 0.5rem;
  color: #fff;
  font-size: 0.8rem;
  font-weight: bold;
}

.movie-genres span:hover {
  cursor: pointer;
  background-color: #455a08;
  box-shadow: 0 0 0.5rem #748708;
}

.active {
  background-color: #67b086;
  font-weight: bolder;
}

.movie-genres span.active {
  background-color: #abc322;
  color: #000;
  font-weight: bolder;
}
</style>
