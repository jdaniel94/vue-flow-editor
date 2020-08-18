<template>
  <div class="hello">
    <h1>UI Editor ...</h1>
    <h3 v-if="false">Canvas area</h3>
    <!-- HTML Editor -->
    <div v-if="false" class="flow-container" @mousemove="moveNode" ref="flowContainer">
      <div
        v-for="(node, ix) in nodes"
        :key="node.id"
        class="item-box"
        :style="{ left: node.x + 'px', top: node.y + 'px' }"
        @mousedown="event => pressNode(ix, event)"
        @mouseup="releaseNode"
      >
        <p>{{node.label}} - {{ ix == currentNode ? 'Activo' : 'Inactivo' }}</p>
      </div>
    </div>
    <h3 v-if="false">SVG area</h3>
    <svg class="flow-container" @mousemove="moveNode" ref="flowContainer">
      <ActionNode
        v-for="(node, ix) in nodes"
        :key="'node-' + ix"
        :x="node.x"
        :y="node.y"
        :width="node.width"
        :text="node.label"
        @mousedown="event => pressNode(ix, event)"
        @mouseup="event => releaseNode(ix, event)"
      ></ActionNode>
      <NodeRelation
        v-for="(relation, ix) in relations"
        :key="'relation-' + ix"
        :from="nodes[relation.from]"
        :to="nodes[relation.to]"
        :direction="relation.direction"
      ></NodeRelation>
    </svg>
    X: {{ currentX }}
    Y: {{ currentY }}
    <code
      style="text-align: left"
      v-if="currentNode != null"
    >
      <pre>{{ nodes[currentNode] }}</pre>
    </code>
  </div>
</template>

<script>
import ActionNode from "./ActionNode";
import NodeRelation from "./NodeRelation";
export default {
  name: "FlowEditor",
  components: {
    ActionNode,
    NodeRelation
  },
  props: {
    /*
    elements: {
      type: Array,
      default: () => [
        [
          { node: 1, type: "box", x: 5, y: 5, label: "Default element" },
          { node: 2, type: "box", x: 25, y: 25, label: "Santo clos" },
          { node: 3, type: "box", x: 25, y: 25, label: "Santo potato" }
        ]
      ],
      required: false
    }
    */
  },
  data: () => ({
    nodes: {
      1: {
        node: 1,
        type: "box",
        x: 5,
        y: 15,
        label: "Default element",
        width: 150,
        height: 50
      },
      2: {
        node: 2,
        type: "box",
        x: 250,
        y: 15,
        label: "Santo clos",
        width: 150,
        height: 50
      },
      3: {
        node: 3,
        type: "box",
        x: 15,
        y: 95,
        label: "Santo potato",
        width: 200,
        height: 50
      }
    },
    relations: [
      { from: 1, to: 2, direction: "->" },
      { from: 3, to: 2, direction: "->" }
    ],
    currentNode: null,
    currentX: 0, // Posicion actual del mouse ...
    currentY: 0,
    startX: 0, // Lugar del click original
    startY: 0,
    originalX: 0, // Posicion original del nodo
    originalY: 0,
    title: "Hii!!",
    flowContainer: null
  }),
  methods: {
    pressNode: function(nodeIx) {
      // Cuando alguien da click en un elemento ... (onmousedown)
      // console.log("Se preciono!!!");
      // console.log(event);
      this.currentNode = nodeIx;

      this.startX = this.currentX;
      this.startY = this.currentY;

      this.originalX = this.nodes[nodeIx].x;
      this.originalY = this.nodes[nodeIx].y;
    },
    moveNode: function(event) {
      const parent = this.$refs.flowContainer.getBoundingClientRect();
      // Cuando mueven el elemento ... (onmousemove ?)
      // console.log("movimiento ....");
      // console.log(event);
      this.currentX = event.x - parent.x;
      this.currentY = event.y - parent.y;

      if (this.currentNode !== null) {
        this.nodes[this.currentNode].x =
          -this.startX + this.currentX + this.originalX;
        this.nodes[this.currentNode].y =
          -this.startY + this.currentY + this.originalY;
      }
      // console.log(event);
    },
    releaseNode: function() {
      // Cuando alguien suelta un elemento ... (onmouseup)
      // console.log("Se libero!!!");
      this.currentNode = null;
      this.startX = 0;
      this.startY = 0;
      this.originalX = 0;
      this.originalY = 0;
    },
    infoNode: e => {
      // Metainfo cuando el mouse esta sobre un elemento ... (onmouseenter)
      console.log("Click!!!");
      console.log(e);
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
.flow-container {
  width: 100%;
  height: 100%;
  border-color: #424ab9;
  margin: 10px;
  border-style: dashed;
  min-height: 250px;
  overflow: hidden;
  box-sizing: border-box;
  position: relative;
}

.item-box {
  border-style: solid;
  border-color: gray;
  position: absolute;
  width: 150px;
  height: 50;
  user-select: none;
}
</style>
