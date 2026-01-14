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
      nodes: []
    };
  },
  methods: {
    // Start the game
    start(){
      this.generate_nodes()
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