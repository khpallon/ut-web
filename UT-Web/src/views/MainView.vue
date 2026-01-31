<template>
  <div class="container">
    <h2 v-for="node in answer">{{ node }}</h2>
    <h2>{{ timer }}</h2>
  </div>
  <div class="container ontop">
    <table>
      <tbody>
        <tr v-for="(node, rowIndex) in nodes" :key="rowIndex">
          <td v-for="(value, colIndex) in node" :key="rowIndex - colIndex" :ref="element => player_location(element, rowIndex, colIndex)" :class="{answer: reveal_answer(rowIndex, colIndex)}" >{{ value }}</td>
        </tr>
    </tbody>
    </table>
  </div>
  <div class="pause" v-if="end">
      <h1 v-if="!win">fail</h1>
      <h1 v-else="win">win</h1>
  </div>
  <div class="container">
      <button @click="start()" @keydown.enter.prevent>Start</button>
  </div>
  
</template>

<script>
export default {
  data() {
    return {
      nodes: [],
      answer: [],
      answer_location: [],
      gameInterval: null,
      timerInterval: null,
      end: false,
      win: false,
      player: null,
      timer: null
    };
  },
  methods: {
    // Start the game
    start(){
      if (this.gameInterval) {
        clearInterval(this.gameInterval);
        this.gameInterval = null;
      }
      this.player = [[5, 4], [5, 5], [5, 6], [5, 7]] // set player coordinates
      this.generate_nodes()
      this.generate_answer()
      this.player_location()
      window.addEventListener('keydown', this.move_player);
      this.end = false;
      this.out_of_time()
      this.update_grid()
    },


    // Finds and reveals the answer location in the grid
    reveal_answer(row, col){
      if (this.end === false){
        return false
      }

      for (let i = 0; i < this.answer_location.length; i++){
        if (this.answer_location[i][0] === row && this.answer_location[i][1] === col){
          return true
        }
      }
    },

    // Sets the player location visually in the grid
    player_location(element, row, col){

      if (element === undefined){
          return
      }

      element.classList.remove('player')
      
      for (let i = 0; i < this.player.length; i++){
        // player location
        if (this.player[i][0] === row && this.player[i][1] === col){
          element.classList.add('player')
        }

        // correct answer location
        if (this.answer_location[i][0] === row && this.answer_location[i][1] === col){

        }

      }
    },

    // Player movement in the grid
    move_player(pressed){
      
      if (pressed.key === "ArrowUp") {
        for (let i = 0; i < this.player.length; i++){
          if (this.player[i][0] === 0){
            this.player[i][0] = 10
          } else {
            this.player[i][0]-- 
          }
        }
      }

      if (pressed.key === "ArrowDown") {
        for (let i = 0; i < this.player.length; i++){
          if (this.player[i][0] === 10){
            this.player[i][0] = 0
          } else {
            this.player[i][0]++ 
          }
        }
      }

      if (pressed.key === "ArrowRight") {
        for (let i = 0; i < this.player.length; i++){

          if (this.player[i][1] === 11 && this.player[i][0] === 10){
            this.player[i][0] = 0
            this.player[i][1] = 0
          } else if (this.player[i][1] === 11){
            this.player[i][0]++
            this.player[i][1] = 0
          } else {
            this.player[i][1]++ 
          }
            
        }
      }

      if (pressed.key === "ArrowLeft") {
        for (let i = 0; i < this.player.length; i++){
          if (this.player[i][1] === 0 && this.player[i][0] === 0){
            this.player[i][0] = 10
            this.player[i][1] = 11
          } else if (this.player[i][1] === 0){
            this.player[i][0]--
            this.player[i][1] = 11
          } else {
            this.player[i][1]-- 
          }
            
        }
      }

      if (pressed.key === "Enter"){
        clearInterval(this.gameInterval);
        clearInterval(this.timerInterval)
        this.gameInterval = null;
        window.removeEventListener('keydown', this.move_player);
        this.end_game()
      }

    },


    // Ends the game and checks for win/loss
    end_game(){
      this.end = true

      for (let i = 0; i < this.player.length; i++){
          if (this.nodes[this.player[i][0]][this.player[i][1]] !== this.answer[i]){
            console.log('lost')
            return
          }

      }
      this.win = true
      console.log('win!')
      
    },

    out_of_time(){
      this.timer = 6

      this.timerInterval = setInterval(() => {
        this.timer--
        if (this.gameInterval && this.timer <= 0){
          this.end = true
          clearInterval(this.gameInterval)
          this.gameInterval = null;
          window.removeEventListener('keydown', this.move_player);
          clearInterval(this.timerInterval)
          this.timerInterval = null;
        }
      }, 1000);

    },

    // Sets an interval for shifting the grid
    update_grid(){
      this.gameInterval = setInterval(() => {
        this.shift_grid();
        this.update_answer_location();
        console.log(this.answer_location[0])
      }, 2000);
    },


    // Updates the answer location in the grid
    update_answer_location(){
      for (let i = 0; i < this.answer_location.length; i++){

        if (this.answer_location[i][1] === 11 && this.answer_location[i][0] === 10){
          this.answer_location[i][0] = 0
          this.answer_location[i][1] = 0
        } else if (this.answer_location[i][1] === 11){
          this.answer_location[i][0]++
          this.answer_location[i][1] = 0
        } else {
          this.answer_location[i][1]++
        }
        
      }
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


    // Generates a random answer from nodes array
    generate_answer(){
      this.answer = [];
      this.answer_location = [];

      let row = Math.floor(Math.random() * 11);
      let col = Math.floor(Math.random() * 12);

      for (let i = 0; i < 4; i++){
        this.answer.push(this.nodes[row][col])
        this.answer_location.push([row, col])

        if (col === 11 && row === 10){
          col, row = 0
        } else if (col === 11){
          col = 0
          row++
        } else {
          col++
        }
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

  .win {
    filter: blur(5px);
  }

  .container {
    display: flex;
    justify-content: center;
  }

  .ontop {
    position: relative;
    text-align: center;  
  }

  table {
    border-collapse: collapse;
  }

  div .pause{
    display: block;
    width: 50%;
    margin: auto;
    place-items: center;
    background-color: rgb(150, 150, 150);
  }

  /* h1 {
    position: absolute;
    inset: 0;         
    z-index: 10;
    justify-content: center;
  } */

  .answer {
    color: blue;
  }

  tr td, h2, h1 {
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