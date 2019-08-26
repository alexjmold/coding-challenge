<template>
  <div id="app">
    <div class="visuals-container">
      <Grid
        :columns="columns"
        :rows="rows"
      />
      <Ship />
    </div>
    <div class="input-container">
      <textarea v-model="gridInput"></textarea>
      <button
        @click="processInput"
      >Go!</button>
    </div>
  </div>
</template>

<script>
import Grid from './components/Grid.vue'
import Ship from './components/Ship.vue'

export default {
  name: 'app',
  components: {
    Grid,
    Ship
  },
  data() {
    return {
      columns: 5,
      rows: 3,
      gridInput: '',
    }
  },
  methods: {
    processInput() {
      // Example input
      // 5 3
      // 1 1 E
      // RFRFRFRF
      const input = this.gridInput;

      // Split all input into separate array instances
      const lines = input.split('\n');

      // World size is always the first line
      const worldSize = lines[0];
      const worldSizeValues = worldSize.split(" ");
      this.columns = parseInt(worldSizeValues[0]);
      this.rows = parseInt(worldSizeValues[1]);

    }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
