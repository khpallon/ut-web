<template>
  <div class="container">
    <table>
      <tbody>
        <tr v-for="(node, rowIndex) in nodes" :key="rowIndex">
          <td v-for="(value, colIndex) in node" :key="rowIndex - colIndex" :ref="element => player_location(element, rowIndex, colIndex)">{{ value }}</td>
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
      updateId: null,
      player: [[5, 4], [5, 5], [5, 6], [5, 7]] // player coordinates
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
      this.player_location()
      this.update_grid()
    },

    player_location(element, row, col){
      console.log([row, col])

      for (let i = 0; i < this.player.length; i++){
        if (this.player[i][0] === row && this.player[i][1] === col){
          element.classList.add('player')
        }
      }
    },

    move_player(){

    },

    // Sets an interval for shifting the grid
    update_grid(){
      this.updateId = setInterval(() => {
        this.shift_grid();
        this.update_player_location();
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
<style scoped>
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

  .player {
    color: green;
  }
</style>