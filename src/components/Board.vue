<template>
  <div
    class="board-container"
    :width="Number(size) * 50"
    :height="Number(size) * 50"
  >
    <div class="square" v-for="i in Number(size)" :key="i">
      <square
        @clicked="checkSquare"
        @hover="checkPool"
        v-for="j in Number(size)"
        :coordToShowFinalCount="coordToShowFinalCount"
        :finalCount="finalCount"
        :animate="animate"
        :pool="pool"
        :mat="mat"
        :row="i"
        :col="j"
        :key="i + j"
      >
      </square>
    </div>
  </div>
</template>
<script>
import Square from "./Square.vue";
export default {
  name: "Board",
  props: [
    "size",
    "mat",
    "coordToShowFinalCount",
    "finalCount",
    "animate",
    "pool",
    "colorHover",
    "colorBackground"
  ],
  components: {
    Square
  },
  methods: {
    checkSquare(coord) {
      this.$emit("clicked", coord);
    },
    checkPool(coord) {
      this.$emit("hover", coord);
    }
  }
};
</script>
<style lang="scss">
.square {
  -webkit-box-shadow: 7px 7px 41px 5px rgba(0, 0, 0, 0.39);
  -moz-box-shadow: 7px 7px 41px 5px rgba(0, 0, 0, 0.39);
  box-shadow: 7px 7px 41px 5px rgba(0, 0, 0, 0.39);
}
</style>
