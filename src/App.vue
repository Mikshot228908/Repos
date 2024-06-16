<template>
  <div>
     <div v-for="ch in characters" :key="ch.id">
          <img :src="ch.image" alt="Character Image"/>
          <h4><p>{{ ch.name }}-{{ ch.status }}</p></h4>
      </div>
  </div>
</template>


<script>
  export default{
      data(){
          return {
              characters:[]
          };
      },
      created(){
          this.fetchCharacters();
      },
      methods: {
          async fetchCharacters(){
              try {
                  const response = await fetch('https://rickandmortyapi.com/api/character');
                  const data = await response.json();
                  this.characters = data.results;
              }catch(error){
                  console.error("Ошибка при получении данных от API",error);
              }
          }
      }
  };
</script>

<style scoped>
 p{
      color:blueviolet;
      font-size: larger;
      font-style: oblique 90deg;
      
 }
</style>