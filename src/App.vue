<template>
  <div id="app">
    <v-app>
      <v-card class="card" elevation="12" width="246">
        <v-navigation-drawer floating permanent>
          <v-list dense rounded>
            <v-list-item @click="startProcess" class="refresh" link>
              <v-list-item-icon>
                <v-icon>mdi-refresh</v-icon>
              </v-list-item-icon>
              <v-list-item-content>
                <v-list-item-title>Generate</v-list-item-title>
              </v-list-item-content>
            </v-list-item>
            <v-list-item class="slider">
              <v-slider max="20" min="1" v-model="size" thumb-label="always" />
            </v-list-item>
            <v-list-item class="color-hover">
              <v-color-picker
                width="200"
                hide-inputs
                hide-mode-switch
                v-model="colorHover"
              ></v-color-picker>
            </v-list-item>
            <v-list-item class="color-background">
              <v-color-picker
                width="200"
                hide-inputs
                hide-mode-switch
                v-model="colorBackground"
              ></v-color-picker>
            </v-list-item>
          </v-list>
        </v-navigation-drawer>
      </v-card>
      <board
        :width="size * 50"
        :height="size * 50"
        class="board"
        v-if="showGrid"
        @clicked="checkSquare"
        @hover="checkPool"
        :coordToShowFinalCount="getCoordsForFinalCount"
        :finalCount="finalCount"
        :animate="animate"
        :pool="pool"
        :mat="mat"
        :size="size"
      ></board>
    </v-app>
  </div>
</template>

<script>
import Board from "@/components/Board.vue";

export default {
  name: "app",
  data() {
    return {
      pointer: [0, 0],
      coordClicked: [0, 0],
      coordsOf1: [],
      pool: [],
      mat: [],
      size: 9,
      showGrid: false,
      finalCount: 0,
      coordToShowFinalCount: [null, null],
      animate: false,
      colorHover: "#a686ff",
      colorBackground: "#ffffff"
    };
  },
  components: {
    Board
  },
  computed: {
    getCoordsForFinalCount() {
      return this.coordToShowFinalCount;
    }
  },
  watch: {
    size() {
      this.finalCount = 0;
      this.coordToShowFinalCount = [null, null];
      this.startProcess();
      this.$forceUpdate();
    },
    colorHover(color) {
      // check if it has customized style on body and remove those
      document.body.children.forEach(element => {
        if (
          element.customStyleName &&
          element.customStyleName == "squareHoverState"
        ) {
          document.body.removeChild(element);
        }
      });
      // create another global style on top of compiled class
      var styleObject = document.createElement("style");
      styleObject.customStyleName = "squareHoverState";
      styleObject.innerHTML = ".hoverState {background-color: " + color + ";}";
      document.body.appendChild(styleObject);
    },
    colorBackground(color) {
      // check if it has customized style on body and remove those
      document.body.children.forEach(element => {
        if (
          element.customStyleName &&
          element.customStyleName == "squareBackgroundColor"
        ) {
          document.body.removeChild(element);
        }
      });
      // create another global style on top of compiled class
      var styleObject = document.createElement("style");
      styleObject.customStyleName = "squareBackgroundColor";
      styleObject.innerHTML = ".empty {background-color: " + color + ";}";
      document.body.appendChild(styleObject);
    }
  },
  mounted() {
    this.startProcess();
  },
  methods: {
    startProcess() {
      this.generateMatrix(this.size);
      this.resetInitialValues();
    },
    resetInitialValues() {
      this.pool = [];
      this.finalCount = 0;
      this.coordsOf1 = [];
    },
    generateMatrix(N) {
      this.mat = [];
      for (let i = 0; i < N; i++) {
        this.mat[i] = [];
        for (let j = 0; j < N; j++) {
          this.mat[i][j] = Math.round(Math.random());
        }
      }
      this.showGrid = true;
    },
    checkSquare(coord) {
      this.animate = false;
      this.resetInitialValues();
      this.coordClicked[0] = coord[0] - 1;
      this.coordClicked[1] = coord[1] - 1;
      this.checkClosersProcess();
    },
    checkPool(coord) {
      this.animate = true;
      this.resetInitialValues();
      this.coordClicked[0] = coord[0] - 1;
      this.coordClicked[1] = coord[1] - 1;
      this.checkClosersProcess();
    },
    checkClosersProcess() {
      if (this.mat[this.coordClicked[0]][this.coordClicked[1]] == 1) {
        // add the first one clicked into the pool list
        this.pool.push([this.coordClicked[0], this.coordClicked[1]]);

        // get coords of 1
        for (let i = 0; i < this.mat.length; i++) {
          for (let j = 0; j < this.mat[i].length; j++) {
            if (this.mat[i][j] == 1) {
              this.coordsOf1.push([i, j]);
            }
          }
        }
        this.checkMatrix();
        if (!this.animate) {
          this.coordToShowFinalCount = [
            this.coordClicked[0],
            this.coordClicked[1]
          ];
        }
      }
    },
    checkMatrix() {
      // If has closest, add to the pool list
      // in the end of the loop, if the pool is bigger than before, go to the next pool index based on length - 1
      // and start the sequence again
      // if the pool length is the same as before, get the pool length and show it onto the clicked coord.

      // get the pool length
      let previousPoolLength = this.pool.length;
      let pointer = this.pool[previousPoolLength - 1];
      // loop to check closest based on 1,0 || 0,1 each pool element
      for (let i = 0; i < this.coordsOf1.length; i++) {
        let r = pointer[0];
        let c = pointer[1];
        // [0,1] or [1,0] to be closest
        let rCheck = Math.abs(this.coordsOf1[i][0] - r); // 0 or 1
        let cCheck = Math.abs(this.coordsOf1[i][1] - c); // 0 or 1
        if ((rCheck == 0 && cCheck == 1) || (rCheck == 1 && cCheck == 0)) {
          // closest
          let addCoord = true;
          // check if it's there in the pool already
          for (let j = 0; j < this.pool.length; j++) {
            if (
              this.pool[j][0] == this.coordsOf1[i][0] &&
              this.pool[j][1] == this.coordsOf1[i][1]
            ) {
              addCoord = false;
            }
          }
          // add it to the pool and force check again based on the last one added
          if (addCoord) {
            this.pool.push([this.coordsOf1[i][0], this.coordsOf1[i][1]]);
            this.checkMatrix();
          }
        }
      }
      if (this.pool.length > previousPoolLength) {
        this.checkMatrix();
      } else {
        this.finalCount = this.pool.length;
      }
    }
  }
};
</script>

<style lang="scss">
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  width: 100%;
  height: 100%;
  min-width: 1100px;
  background-color: lightsteelblue;
  .card {
    margin-left: 10px;
    margin-top: 10px;
    padding-bottom: 13px;
  }
  .slider {
    margin-top: 30px;
  }
  .board {
    margin-top: -570px;
    text-align: center;
  }
}
</style>
