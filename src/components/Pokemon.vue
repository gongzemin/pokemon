<template>
  <div class="pokemon" :class="{ 'is-backwards': isBackwards }">
    <div class="content">
      <div class="name">{{ pokemon && pokemon.name }}</div>
      <div class="hp">
        <progress class="progress" :class="healthColor" :value="health" :max="fullHealth"></progress>
      </div>
      <div class="moves" v-if="showAttacks">
        <button
          @click="handleAttack"
          v-for="(move, index) in movesList"
          :key="index"
        >{{ move.move.name }}</button>
      </div>
    </div>

    <div class="image">
      <img :src="image">
    </div>
  </div>
</template>

<script>
export default {
  props: ["pokemon", "isBackwards", "health", "showAttacks"],
  methods: {
    // findHealth() {
    //   const hpStat = this.pokemon.stats.find(stat => stat.stat.name === "hp");
    //   return hpStat.base_stat;
    // }
    handleAttack() {
      console.log('attacking on the child component')
      this.$emit('attackMade', { damage: 10 })
    }
  },
  computed: {
    image() {
      return this.isBackwards
        ? this.pokemon && this.pokemon.sprites.back_default
        : this.pokemon && this.pokemon.sprites.front_default;
    },

    fullHealth() {
      // pikachu base_stat 35
      // charizard base_stat 78
      //return this.findHealth();
      //debugger
      if (this.pokemon) {
        const hpStat = this.pokemon.stats.find(stat => stat.stat.name === "hp");

        return hpStat.base_stat;
      }
    },
    healthColor() {
      if (this.health > 0.8 * this.fullHealth) return "is-success";
      if (this.health > 0.5 * this.fullHealth) return "is-warning";
      return "is-danger";
    },
    movesList() {
      return this.pokemon.moves.slice(0, 10)
    }
  }

};
</script>

<style  scoped>
.pokemon {
  display: flex;
}

.content {
  text-align: right;
}
.name {
  font-size: 18px;
  margin-bottom: 5px;
}
.pokemon.is-backwards {
  flex-direction: row-reverse;
}

.hp {
  width: 150px;
}

.image {
  min-width: 100%;
}
</style>
