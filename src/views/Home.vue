<template>
  <div id="app" class="hero is-fullheight is-link is-bold">
    <div class="hero-body">
      <div class="container">
        <div class="pokemon-battle">
          <div v-if="attacking" class="attacking">
            <h3>Who are you attacking?</h3>
            <div v-for="(pokemon, index) in canAttack" :key="index">
              <button @click="handleTargetChosen(pokemon.pokemon.name)">{{ pokemon.pokemon.name }}</button>
            </div>
          </div>
          <div v-for="(pokemon, index) in pokemons" v-bind:key="index">
            <Pokemon
              :pokemon="pokemon.pokemon"
              :health="pokemon.health"
              @attackMade="handleAttack(index, $event)"
              :isBackwards="index === 1"
              :showAttacks="index === attacker"
            />
          </div>
          <!-- <Pokemon
            :pokemon="pokemons[1].pokemon"
            :health="pokemons[1].health"
            :isBackwards="true"
            @attackMade="handleAttack('pokemonOne', $event)"
          />-->
        </div>
        <!-- List two pokemon (image + name)
          List each pokemons moves

          Click a move to attack
            - damage the other pokemon
        - pokemon has a health bar-->
      </div>
    </div>
  </div>
</template>

<script>
import Pokemon from "@/components/Pokemon";

export default {
  name: "App",
  components: {
    Pokemon
  },
  data() {
    return {
      winner: null,
      attacking: false,
      attacker: 0,
      damage: '',
      pokemons: [
        {
          pokemon: null,
          health: null
        },
        {
          pokemon: null,
          health: null
        },
        {
          pokemon: null,
          health: null
        }
      ]
    };
  },
  methods: {
    getPokemon(name) {
      return fetch(`https://pokeapi.co/api/v2/pokemon/${name}`).then(resp =>
        resp.json()
      );
    },
    findPokemonHealth(pokemon) {
      const hpStat = pokemon.stats.find(stat => stat.stat.name === 'hp');
      return hpStat.base_stat;
    },
    handleAttack(attacker, e) {
      // console.log(attacker, e);
      // this.pokemonTwo.health = this.pokemonTwo.health - e.damage
      // this.attacking.health = this.attacking.health - e.damage

      //this[attacking].health = this[attacking].health - e.damage
      this.attacking = true;
      this.attacker = attacker
      this.damage = e.damage
    },
    handleTargetChosen(targetName) {
      const targetIndex = this.pokemons.findIndex(pokemon => pokemon.pokemon.name === targetName)
      this.attacking = false;
      this.pokemons[targetIndex].health = this.pokemons[targetIndex].health - this.damage

      // cycle the pokemon attacker
      this.attacker =
        this.pokemons.length === this.attacker + 1 ? 0 : this.attacker + 1;
    }
  },
  mounted() {
    this.getPokemon("pikachu").then(data => {
      this.pokemons[0].pokemon = data;
      this.pokemons[0].health = this.findPokemonHealth(data);
    });
    this.getPokemon("charizard").then(data => {
      this.pokemons[1].pokemon = data;
      this.pokemons[1].health = this.findPokemonHealth(data);
    });
    this.getPokemon("ditto").then(data => {
      this.pokemons[2].pokemon = data;
      this.pokemons[2].health = this.findPokemonHealth(data);
    });
  },
  computed: {
    canAttack() {
      return this.pokemons.filter((pokemon, index) => index !== this.attacker)
    }
  }
};
</script>

<style>
.pokemon-battle {
  width: 100%;
  max-width: 250px;
  margin: 0 auto;
}

.attacking {
  position: fixed;
  width: 300px;
  height: 200px;
  background: #fff;
  z-index: 10;
  padding: 30px;
  text-align: center;
  color: #333;
}
</style>
