<template>
  <div class="hello">
    <h1>PokeGuía</h1>
    <label for="inputPokemon">Nombre:</label>
    <input
      v-model="newPokemon"
      type="text"
      id="inputPokemon"
      placeholder="Ingresa tu pokémon"
    />
    <button @click="getPokemon">Buscar</button>
    <p>{{ error }}</p>
    <img :src="imagenPokemon" />
    <h2>Movimientos</h2>
    <ul>
      <li v-for="(ability, index) in abilitiesPokemon" :key="index">
        {{ ability }}
        <br />
      </li>
    </ul>
    <h2>Habilidades</h2>
    <ul>
      <li v-for="(move, index) in movesPokemon" :key="index">
        {{ move }}
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  name: 'PokeApi',
  data(){
    return {
      dataPokemon: null,
      newPokemon: '',
      error: ''
    }
  },
  methods: {
    getPokemon(){
      if(this.newPokemon){
        let namePokemon = this.newPokemon.toLowerCase();
        this.fetchPokemon(namePokemon);
      }
      else{
        this.getError('empty');
        this.cleanPokemon();
      }
    },
    async fetchPokemon(name){
      try{
        let path = `https://pokeapi.co/api/v2/pokemon/${name}`
        let request = await fetch(path);        
        let response = await request.json();
        this.dataPokemon = response;
        this.cleanError();
      }
      catch(err){
        console.log(err);
        this.getError('fallo');
      }     
    },
    getError(entry){
      this.cleanError();
      this.cleanPokemon();
      switch (entry) {
        case 'fallo':
          this.error = `No se encontro Pokemon ${this.newPokemon} consultado`
          break;
        case 'empty':
          this.error = `No ingreso ningun nombre de Pokemon`
          break;
      }
    },
    cleanError(){
      this.error = '';
    },
    cleanPokemon(){
      this.dataPokemon = null;
    },
  },
  created(){
    this.fetchPokemon('pikachu')
  },
  computed:{
    imagenPokemon: function(){
      if(this.dataPokemon) return this.dataPokemon.sprites.other.dream_world.front_default;
      return '';
    },
    movesPokemon: function(){
      if(this.dataPokemon) return this.dataPokemon.moves.map( m => m.move.name);
      return [];
    },
    abilitiesPokemon: function(){
      if(this.dataPokemon) return this.dataPokemon.abilities.map( a => a.ability.name );
      return [];
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  padding: 0;
}

</style>
