<template>
  <div class="card">
    <img class="card__image-pokemon" :src="pokemonUrl" :alt="pokemonName" :title="pokemonName">
    <div class="card__box-content">
      <span v-text="pokemonName"></span> <span>#{{ pokemonID }}</span>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Pokemon',
  props: {
    pokemonUrl: {
      type: String,
      required: true
    },

    pokemonName: {
      type: String,
      required: true
    },

    pokemonID: {
      type: Number,
      required: false
    },
  },
}
</script>

<style lang="scss">
.card {
  display: grid;
  grid-template: 1fr / 1fr;
  height: inherit;

  .card__image-pokemon {
    width: auto;
    display: block;
    margin: 0 auto;
    align-self: center;
  }

  .card__box-content {
    width: 100%;
    display: flex;
    justify-content: space-between;

    :first-child {
      padding: 0 0 15px 15px;
      text-transform: capitalize;
    }

    :last-child {
      padding: 0 15px 15px 0;
    }
  }
}
</style>
