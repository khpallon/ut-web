<template>
  <div>
    <button @click="start()">Start</button>
  </div>
  <table>
    <tbody>
      <tr v-for="(node, rowIndex) in nodes" :key="rowIndex">
        <td v-for="(value, colIndex) in node" :key="rowIndex - colIndex">{{ value }}</td>
      </tr>
  </tbody>
  </table>
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
      this.display()
    },

    // Generates a 10 x 11 grid of two combined uppercase letters and returns it
    generate_nodes(){ // COMPONENT
      this.nodes = [];
      for (let i = 0; i < 11; i++){
        let row = [];
        for (let j = 0; j < 10; j++){
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