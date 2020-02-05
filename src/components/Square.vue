<template>
  <div
    class="square"
    v-bind:class="getChanges"
    @mouseover="checkPool"
    @click="checkSquare"
  >
    <span class="square-text">{{
      showFinalCount == 0 ? "" : showFinalCount
    }}</span>
  </div>
</template>
<script>
export default {
  name: "Square",
  props: [
    "row",
    "col",
    "mat",
    "coordToShowFinalCount",
    "finalCount",
    "animate",
    "pool",
    "colorHover",
    "colorBackground"
  ],
  data() {
    return {
      showFinalCount: 0
    };
  },
  methods: {
    checkSquare() {
      this.$emit("clicked", [this.row, this.col]);
    },
    checkPool() {
      this.$emit("hover", [this.row, this.col]);
    },
    squareColor() {
      if (this.animate && this.pool.length > 0) {
        for (let i = 0; i < this.pool.length; i++) {
          if (
            this.pool[i][0] == this.row - 1 &&
            this.pool[i][1] == this.col - 1
          ) {
            return "hoverState";
          }
        }
      }
      if (this.mat[this.row - 1][this.col - 1] == 0) {
        return "empty";
      } else {
        return "filled";
      }
    }
  },
  watch: {
    coordToShowFinalCount(coord) {
      if (coord[0] == this.row - 1 && coord[1] == this.col - 1) {
        this.showFinalCount = this.finalCount;
      } else {
        this.showFinalCount = 0;
      }
    }
  },
  computed: {
    getChanges() {
      let color = this.squareColor();
      return color;
    }
  }
};
</script>

<style lang="scss">
.square {
  width: 50px;
  height: 50px;
  display: inline-block;
  border: solid rgba(56, 56, 56, 0.5) 1px;
  border-radius: 10px;
  margin-right: 0px;
  margin-top: -5px;
}
.empty {
  background-color: #fff;
}
.filled,
.filled:hover {
  background-color: rgb(166, 134, 255);
  cursor: pointer;
  text-align: center;
  .square-text {
    display: table;
    position: relative;
    top: 12px;
    color: rgb(255, 255, 255);
    size: 2.5em;
    margin: auto;
  }
}

.hoverState {
  background-color: #fcff56;
  cursor: pointer;
  text-align: center;
  .square-text {
    display: table;
    position: relative;
    top: 12px;
    color: rgb(255, 255, 255);
    size: 2.5em;
    margin: auto;
  }
}
</style>
