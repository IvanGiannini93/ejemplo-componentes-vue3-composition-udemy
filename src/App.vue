<script setup>
  import { computed, onMounted, ref } from "vue";

  import ButtonCounter from "./components/ButtonCounter.vue";
  import BlogPost from "./components/BlogPost.vue";
  import PaginatePost from "./components/PaginatePost.vue"
  import LoadingSpinner from "./components/LoadingSpinner.vue"

  const posts= ref([]);
  const postForPage = 10;
  const inicio = ref(0);
  const fin = ref(postForPage);
  const loading = ref(true);

  const favorito = ref('');

  const cambiarFavorito = (title) => {
    favorito.value = title;
  };

  const next = () => {
    inicio.value += postForPage;
    fin.value += postForPage;
  };

  const prev = () => {
    inicio.value -= postForPage;
    fin.value -= postForPage;
  };

  onMounted(async () => {
    fetchData();
  });
/* fetch('https://jsonplaceholder.typicode.com/posts')
  .then(res => res.json())
  .then(data => posts.value = data)
  .catch((e) => crossOriginIsolated.log(e))
  .finally(() => loading.value = false);
  .finally(() => {
    setTimeout(() => {
      loading.value = false
    }, 2000
    )
  });   una prueba para ver como funciona el spinner*/ 

  const fetchData = async () => {
    try{
      const res = await fetch("https://jsonplaceholder.typicode.com/posts");
      posts.value = await res.json();
    } catch(error){
      console.log(error);
    } finally{
      loading.value = false;
    }
  };

  const maxLenght = computed(() => posts.value.length)
</script>

<template>
  <LoadingSpinner v-if="loading"/>
  <div class="container" v-else>
    <h1>App</h1>
    <h2>Mis posts favoritos: {{ favorito }}</h2>
    
    <PaginatePost
       @next="next" 
       @prev="prev" 
       :inicio="inicio" 
       :fin="fin"
       :maxLenght="maxLenght"
       class="mb-2" />

    <BlogPost
      v-for="post in posts.slice(inicio, fin)"
      :key="post.id" 
      :title="post.title"
      :id="post.id"
      :body="post.body"
      class="mb-2"
      @cambiarFavoritoNombre="cambiarFavorito"/>
  </div>
</template>
