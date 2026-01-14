<template>
  <div class="container">
    <table>
      <tbody>
        <tr v-for="(node, rowIndex) in nodes" :key="rowIndex">
          <td v-for="(value, colIndex) in node" :key="rowIndex - colIndex">{{ value }}</td>
        </tr>
    </tbody>
    </table>
  </div>
  <div class="container">
      <button @click="start()">Start</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      nodes: [],
      updateId: null 
    };
  },
  methods: {
    // Start the game
    start(){
      if (this.updateId) {
        clearInterval(this.updateId);
        this.updateId = null;
      }

      this.generate_nodes()
      this.update_grid()
    },

    // Sets an interval for shifting the grid
    update_grid(){
      this.updateId = setInterval(() => {
        this.shift_grid();
      }, 2000);
    },

    // Generates a 10 x 11 grid of two combined uppercase letters and returns it
    generate_nodes(){ // COMPONENT
      this.nodes = [];
      for (let i = 0; i < 11; i++){
        let row = [];
        for (let j = 0; j < 12; j++){
          row.push(this.generate_random_letter())
        }
        this.nodes.push(row)
      }
    },

    // Generates a random uppercase letter and returns it
    generate_random_letter(length = 2){ // COMPONENT
     return Array.from({ length }, () =>
      String.fromCharCode(Math.floor(Math.random() * 26) + 65)
      ).join('');
    },


    // Shifts all nodes to the right in the grid
    shift_grid(){
      for (let i = 0; i < 11; i++){
        const last = this.nodes[i].pop()
        if (i+1 === 11){
          this.nodes[0].unshift(last)
        } else {
          this.nodes[i+1].unshift(last)
        }
      }
    }
  }
}
</script>
<style>
  .container {
    display: flex;
    justify-content: center;
  }

  tr td {
    font-family: monospace;
    font-size: xx-large;
    padding: 0.5rem;
  }
  table {
    background-color: rgb(150, 150, 150);
  }
</style>