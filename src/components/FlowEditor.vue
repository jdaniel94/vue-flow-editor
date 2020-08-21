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
      <g>
        <!-- Nodos de accion -->
        <ActionNode
          v-for="(node, ix) in nodes"
          :key="'node-' + ix"
          :x="node.x"
          :y="node.y"
          :width="node.width"
          :text="node.label + '[' + node.node + ']'"
          @mousedown="event => pressNode(ix, event)"
          @mouseup="event => releaseNode(ix, event)"
          @click-node-anchor="event => nodeAnchor(ix, event)"
        ></ActionNode>
        <!-- Relaciones entre nodos -->
        <NodeRelation
          v-for="(relation, ix) in relations"
          :key="'relation-' + ix"
          :from="nodes[relation.from]"
          :to="nodes[relation.to]"
          :direction="relation.direction"
        ></NodeRelation>
      </g>
      <!-- Agregar elementos -->
      
      <g>
        <ActionNode
          :x=20
          :y=20
          :width=150
          @mousedown="event => pressNode(-1, event)"
          @mouseup="event => releaseNode(-1, event)"
          text="New Action Node"
        ></ActionNode>
        <path
          d="M 200 0 L 200 250"
          fill="none"
          stroke="blue"
          stroke-dasharray="5,5"
          stroke-width="1.75"
          stroke-miterlimit="10"
          pointer-events="stroke"
        ></path>
      </g>
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
        x: 300,
        y: 15,
        label: "Default element",
        width: 150,
        height: 50
      },
      2: {
        node: 2,
        type: "box",
        x: 600,
        y: 15,
        label: "Santo clos",
        width: 150,
        height: 50
      },
      3: {
        node: 3,
        type: "box",
        x: 300,
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
    newNode: false,
    currentNodeAnchor: null, // Anclaje seleccionado para ser unido
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
      if (nodeIx > -1) {
        this.currentNode = nodeIx;
      } else {
        nodeIx = Math.max(...Object.keys(this.nodes)) + 1;
        this.$set(this.nodes, nodeIx, {
          node: 1,
          type: "box",
          x: 20,
          y: 20,
          label: "Default element",
          width: 150,
          height: 50
        })

        this.nodes[nodeIx].node = nodeIx;
        this.currentNode = nodeIx;
        this.newNode = true;
      }

      this.startX = this.currentX;
      this.startY = this.currentY;

      this.originalX = this.nodes[nodeIx].x;
      this.originalY = this.nodes[nodeIx].y;
    },
    moveNode: function(event) {
      const parent = this.$refs.flowContainer.getBoundingClientRect();
      // Cuando mueven el elemento ... (onmousemove ?)
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

      // Si x es menor que 300 se elimina ...
      if (this.currentX < 300) {
        // Elimino relaciones
        while(this.relations.findIndex(e => e.from == this.currentNode || e.to == this.currentNode) !== -1) {
          var delIx = this.relations.findIndex(e => e.from == this.currentNode || e.to == this.currentNode)
          this.relations.splice(delIx,1);
        }
        
        // Elimino el nodo
        delete this.nodes[this.currentNode];
      }

      this.currentNode = null;
      this.startX = 0;
      this.startY = 0;
      this.originalX = 0;
      this.originalY = 0;
      this.newNode = false;
    },
    nodeAnchor(nodeIx, event) {
      // Only lines from out to in ...
      if (event == 'out0') {
        this.currentNodeAnchor = nodeIx;
      } else if (event == 'in0' && this.currentNodeAnchor !== null && this.currentNodeAnchor !== nodeIx) {
        const count = this.relations.filter(relation => relation.from == this.currentNodeAnchor && relation.to == nodeIx);
        if (count.length == 0) {
          this.relations.push(
            { from: this.currentNodeAnchor, to: nodeIx, direction: "->" }
          )
          console.log('New relation');
        } else {
          console.log('Duplicated relation');
        }
      } else if ( this.currentNodeAnchor !== null) {
        this.currentNodeAnchor = null;
      }
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
