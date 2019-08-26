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
      shipsInformation: [],
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
      let lines = input.split('\n');

      // World size is always the first line
      const worldSize = lines[0];
      const worldSizeValues = worldSize.split(" ");
      this.columns = parseInt(worldSizeValues[0]);
      this.rows = parseInt(worldSizeValues[1]);

      // Remove instructions and empty lines from array
      lines.shift();
      lines = lines.filter(l => l !== "");

      // Now we can assume that each 2 lines is a start position, and list of instructions.
      let shipInformationCounter = 0;
      this.shipsInformation[0] = {};
      for (let i = 0; i < lines.length; i += 1) {
        if (!this.shipsInformation[shipInformationCounter]) {
          this.shipsInformation[shipInformationCounter] = {};
        }

        if (i % 2 === 0) {
          this.shipsInformation[shipInformationCounter].startPosition = lines[i];
        } else {
          this.shipsInformation[shipInformationCounter].instructions = lines[i];
          shipInformationCounter += 1;
        }
      }
    },
    calculatePosition(shipInformation) {
      const startPosition = shipInformation.startPosition;
      const instructions = shipInformation.instructions;
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
