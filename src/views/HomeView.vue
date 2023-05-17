<template>
  <img src="@/assets/images/logo.png" alt="Rep Analysis Logo" class="logo">

  <form class="search" method="get" action="/report">
    <input type="text" class="search-field" placeholder="Qual empresa gostaria de analisar?" spellcheck="false" v-model="tweet">

    <div class="search-button-wrapper">
      <button type="submit" class="search-button" @click.prevent="">
        <vue-feather type="search" class="search-icon"></vue-feather>
      </button>
    </div>
  </form>
</template>

<script>
import VueFeather from 'vue-feather';
import "@/assets/styles/home.css";

export default {
  name: 'HomeView',
  components: {
    VueFeather
  }
}
</script>
