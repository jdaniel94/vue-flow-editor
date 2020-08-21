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
    <g :transform="`rotate(${angle}, ${this.to.x - 5}, ${this.to.y + this.to.height / 2} )`">
      <path
        :d="endArrow"
        fill="#000000"
        stroke="#000000"
        stroke-width="1.75"
        stroke-miterlimit="10"
        pointer-events="all"
      ></path>
    </g>
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

      const toX = this.to.x - 5;
      const toY = this.to.y + this.to.height / 2;
      return `M ${fromX} ${fromY} L ${toX} ${toY}`; // M 156 40 L 252 40
    },
    angle: function () {
      const fromX = this.from.x + this.from.width;
      const fromY = this.from.y + this.from.height / 2;

      const toX = this.to.x;
      const toY = this.to.y + this.to.height / 2;

      // var vector1 = {x: line1.x2 - line1.x1, y: line1.y2 - line1.y1};
      // var vector2 = {x: line2.x2 - line2.x1, y: line2.y2 - line2.y1};
      // var vector1 = {x: fromX - toX, y: fromY - toY};
      var vector1 = {x: toX - fromX, y: toY - fromY};
      var vector2 = {x: toX - toX, y: toY - 0};
      return ( ( (Math.atan2(vector2.y, vector2.x) - Math.atan2(vector1.y, vector1.x)) * 180 / Math.PI ) - 90) * -1;
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
