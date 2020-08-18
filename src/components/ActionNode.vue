<template>
  <g transform="translate(0.5,0.5)" style="visibility: visible; cursor: move;">
    <!-- Cuadrado -->
    <rect
      :x="x"
      :y="y"
      :width="width"
      :height="height"
      rx="8"
      ry="8"
      fill="#ffffff"
      stroke="#000000"
      pointer-events="all"
      @mousedown="event => $emit('mousedown', event)"
      @mouseup="event => $emit('mouseup', event)"
    ></rect>
    <!-- Contenido del nodo -->
    <text
      :x="x + 12"
      :y="y + 15"
      font-family="Verdana"
      font-size="14"
      fill="black"
      style="pointer-events: none;"
    >{{ text }}</text>
    <!-- Anclaje de entrada -->
    <circle
      :cx="x + 0"
      :cy="y + (this.height / 2)"
      r="1.5"
      stroke="blue"
      fill="blue"
      stroke-width="3"
    />
    <!-- Anclaje de salida -->
    <circle
      :cx="x + this.width"
      :cy="y + (this.height / 2)"
      r="1.5"
      stroke="gray"
      fill="gray"
      stroke-width="3"
    />
  </g>
</template>

<script>
export default {
  name: "ActionNode",
  props: {
    text: {
      type: String,
      default: ""
    },
    x: {
      type: Number,
      required: true
    },
    y: {
      type: Number,
      required: true
    },
    width: {
      type: Number,
      required: false,
      default: 150
    },
    height: {
      type: Number,
      required: false,
      default: 50
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
