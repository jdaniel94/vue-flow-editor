<template>
  <g style="visibility: visible; cursor: row-resize;">
    <!--  -->
    <path
      :d="linePath"
      fill="none"
      stroke="#000000"
      stroke-width="1.75"
      stroke-miterlimit="10"
      pointer-events="stroke"
    ></path>
    <!-- Flecha [END]  -->
    <path
      :d="endArrow"
      fill="#000000"
      stroke="#000000"
      stroke-width="1.75"
      stroke-miterlimit="10"
      pointer-events="all"
    ></path>
  </g>
</template>

<script>
export default {
  name: "NodeRelation",
  props: {
    from: {
      type: Object,
      required: true
    },
    to: {
      type: Object,
      required: true
    },
    direction: {
      type: String,
      required: false,
      default: "-"
    }
  },
  data: () => ({
    x: 252,
    y: 40
  }),
  methods: {
    makeArrowPath: function(x, y, scale) {
      const p1X = -30 * scale;
      const p1Y = -10 * scale;
      const p2X = 10 * scale;
      const p2Y = 10 * scale;
      const p3X = -10 * scale;
      const p3Y = 10 * scale;
      return `M ${x} ${y} l ${p1X} ${p1Y} l ${p2X} ${p2Y} l ${p3X} ${p3Y} Z`;
    }
  },
  computed: {
    startArrow: function() {
      return this.makeArrowPath(this.x - 5, this.y, -0.3);
    },
    endArrow: function() {
      return this.makeArrowPath(
        this.to.x - 5,
        this.to.y + this.to.height / 2,
        0.3
      );
    },
    linePath: function() {
      const fromX = this.from.x + this.from.width;
      const fromY = this.from.y + this.from.height / 2;

      const toX = this.to.x - 12;
      const toY = this.to.y + this.to.height / 2;
      return `M ${fromX} ${fromY} L ${toX} ${toY}`; // M 156 40 L 252 40
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
