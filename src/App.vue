<template>
  <div id="app">
    <div
      class="visuals-container"
      :style="{
        width: `${columns * gridItemSize}px`,
        height: `${rows * gridItemSize}px`
      }"
    >
      <Grid
        :columns="columns"
        :rows="rows"
        :grid-item-size="gridItemSize"
      />
      <Ship
        ref="ship"
        :grid-item-size="gridItemSize"
      />
    </div>
    <div class="input-container">
      <textarea v-model="gridInput"></textarea>
      <br>
      <button
        @click="processInput"
      >Go!</button>
      <div
        class="output"
        :class="{ active: outputActive }"
      >
        <div class="close" @click="outputActive = false;">x</div>
        <div class="inner">
          <b>Output</b>
          <div
            class="output-row"
            v-for="(item, index) in output"
            :key="index"
          >
            <p v-html="output[index]"></p>
            <button @click="playAnimation(index)">Play animation</button>
          </div>
        </div>
      </div>
    </div>
    <div
      class="show-hide-output"
      @click="outputActive = !outputActive"
    >
      <p v-if="outputActive">
        Hide output
      </p>
      <p v-else>
        Show output
      </p>
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
      gridItemSize: 100,
      shipsInformation: [],
      timeline: null,
      calculatedPositions: null,
      outputActive: false,
      output: '',
      gridInput:
`5 3
1 1 E
RFRFRFRF`,
    }
  },
  methods: {
    processInput() {
      // Get input from v-model
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

      // Get all calculations for each set
      this.calculatedPositions = [];
      for (let i = 0; i < this.shipsInformation.length; i += 1) {
        const calculation = this.calculatePosition(this.shipsInformation[i]);

        this.calculatedPositions[i] = calculation;
      }

      // Show all this information in the output
      this.updateShipOutput();
    },
    calculatePosition(shipInformation) {
      const startPosition = shipInformation.startPosition;
      const instructions = shipInformation.instructions;
      let coordinatesString = startPosition.split(' ').join('');
      let isLost = false;
      const steps = [];
      let c = {
        x: parseInt(coordinatesString[0]),
        y: parseInt(coordinatesString[1]),
        direction: coordinatesString[2],
      };

      let initialPosition = Object.assign({}, c);

      for (let i = 0; i < instructions.length; i += 1) {
        // Use a temporary variable to check if our ship gets lost
        const char = instructions[i];
        const tempC = {};
        tempC.x = c.x;
        tempC.y = c.y;
        tempC.direction = c.direction;
        tempC.instruction = char;

        if (char === 'R' || char === 'L') {
          const direction = tempC.direction;
          const newDirection = this.rotate(direction, char);
          tempC.direction = newDirection;
        } else if (char === 'F') {

          switch(tempC.direction) {
            case 'N':
              tempC.y += 1;
              break;
            case 'E':
              tempC.x += 1;
              break;
            case 'S':
              tempC.y -= 1;
              break;
            case 'W':
              tempC.x -= 1;
              break;
          }

          if (tempC.x > this.columns || tempC < 0
              || tempC.y > this.rows || tempC.y < 0) {
            isLost = true;
            break;
          }
        }

        c = tempC;
        steps.push(c);
      }

      const calculationOuput = {
        finalPosition: c,
        isLost,
        steps,
        initialPosition
      };

      return calculationOuput;
    },
    rotate(currentDirection, rotation) {
      const orientations = ['N', 'E', 'S', 'W'];
      let currentOrientationIndex = orientations.indexOf(currentDirection);

      if (rotation === 'R') {
        currentOrientationIndex += 1;
        if (currentOrientationIndex > (orientations.length - 1)) {
          currentOrientationIndex = 0;
        }
      } else if (rotation === 'L') {
        currentOrientationIndex -= 1;
        if (currentOrientationIndex < 0) {
          currentOrientationIndex = (orientations.length - 1);
        }
      }

      return orientations[currentOrientationIndex];
    },
    updateShipOutput() {
      this.output = [];

      for (let i = 0; i < this.calculatedPositions.length; i += 1) {
        const pos = this.calculatedPositions[i];
        let lostString = '';
        if (pos.isLost) lostString = 'LOST';

         this.output[i] = `<b>Ship ${i + 1}:</b> ${pos.finalPosition.x} ${pos.finalPosition.y} ${pos.finalPosition.direction} ${lostString}<br>`;
      }

      this.outputActive = true;
    },
    playAnimation(index) {
      this.outputActive = false;
      this.$refs.ship.createAnimation(this.calculatedPositions[index]);
    }
  }
}
</script>

<style lang="scss">

@import url('https://fonts.googleapis.com/css?family=Work+Sans&display=swap');


body, html {
  background-color: #2E294E;
  font-family: 'Work Sans', sans-serif;
}

#app {
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #fff;
  margin-top: 60px;
}

textarea {
 font-family: 'Work Sans', sans-serif;
 width: 100%;
 max-width: 350px;
 background: transparent;
 border: 1px solid #fff;
 border-radius: 10px;
 color: #fff;
 padding: 20px;
 display: inline-block;
 font-size: 18px;
 min-height: 80px;
}

button {
  background-color: #1B998B;
  color: #fff;
  font-family: 'Work Sans', sans-serif;
  border: none;
  border-radius: 10px;
  padding: 10px 20px;
  font-size: 25px;
  cursor: pointer;
  // margin-top: 30px;
}

.visuals-container {
  position: relative;
  margin: 50px auto;
}

.output {
  visibility: 0;
  opacity: 0;
  transition: 200ms opacity cubic-bezier(0.215, 0.61, 0.355, 1), 200ms visibility cubic-bezier(0.215, 0.61, 0.355, 1);
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.3);
  pointer-events: none;

  .inner {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    background-color: #fff;
    color: #333;
    display: inline-block;
    border-radius: 10px;
    padding: 10px 20px;
  }

  &.active {
    opacity: 1;
    visibility: visible;
    pointer-events: auto;
  }

  .close {
    position: absolute;
    top: 10px;
    right: 10px;
    cursor: pointer;
    font-size: 25px;
  }
}

.show-hide-output {
  position: fixed;
  bottom: 15px;
  right: 15px;
  cursor: pointer;
}
</style>
