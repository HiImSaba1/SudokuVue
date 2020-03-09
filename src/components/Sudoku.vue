<template>
  <div class="sudoku">
    <div class="row">
    <h2>Sudoku</h2>
    <strong>{{formattedTime}}</strong>
    <select v-model="difficulty" @change="generatePuzzle()">
      <option
      v-for="(display , level) in levels" :key="level" :value="level"
      >
        {{display}}
      </option>
    </select>
    </div>
    <div class="box">
      <div class="grid">
        <div class="row" v-for="(row, rowIndex) in puzzle" :key="rowIndex">
          <div
            class="cell"
            :class="{
              'border-right': colIndex === 2 || colIndex === 5,
              'border-bottom': rowIndex === 2 || rowIndex === 5,
              'original': cell.original,
              'active' : activeRow === rowIndex && activeCol ===colIndex,
              'invalid': cell.value && isCellInvalid(rowIndex,colIndex,cell.value)
            }"
            v-for="(cell, colIndex) in row"
            :key="colIndex" @click="setCellActive(rowIndex ,colIndex ,cell.original)"
          >
            {{ cell.value }}
          </div>
        </div>
      </div>
      <div class="row">
          <button
          type="button" class="btn" v-for="value in Array(9).keys()" :key="value"
          :disabled="activeRow === -1 || activeCol === -1"
          @click="setCellValue(value + 1)"
          >
          {{value + 1}}
          </button>
      </div>

    </div>
  </div>
</template>
<script>
import { sudoku } from "sudoku.js/sudoku.js";
export default {
  name: "Sudoku",
  data() {
    return {
      puzzle: [],
      difficulty: "hard",
      activeRow: -1,
      activeCol: -1,
      levels:{
        'easy':      'Easy',
        'medium':    'Medium',
        'hard':      'Hard',
        'very-hard': 'Very Hard',
        'insane':    'Insane',
        'inhuman':   'Inhuman',
      },
      seconds: 0, 
      timer: null,
    }
  },
  mounted() {
    this.generatePuzzle();
  },
  computed:{
    formattedTime(){
      let min = Math.floor(this.seconds / 60 )
      let sec = this.seconds % 60

      if (min < 10){
        min = `0${min}`
      }
       if (sec < 10){
        sec = `0${sec}`
      }
      return `${min}:${sec}`
    }
  },
  methods: {
    generatePuzzle() {
      const boardString = sudoku.generate(this.difficulty)
      this.puzzle = sudoku.board_string_to_grid(boardString)
      .map(row => {
          return row.map(cell => {
              return {
                  value: cell !== '.' ? parseInt(cell) : null,
                  original: cell !== '.'
              }
          })
      })
      this.seconds = 0
      clearInterval(this.timer)
      this.timer = setInterval(() =>{
        this.seconds ++
      },1000)
    },
    setCellActive(row,col,original){
      if (original){
        return
      } 
      if(this.activeRow === row && this.activeCol === col){
           this.activeRow = -1
           this.activeCol = -1
           return
      }
      this.activeRow = row
      this.activeCol = col
    },
    setCellValue(value){
      this.puzzle[this.activeRow][this.activeCol].value = value
      this.activeRow = -1
      this.activeCol = -1
      if(this.isGameComplete()){
        const msg =[
          'Success!',
          '',
          `Difficulty: ${this.levels[this.difficulty]}`,
          `Time: ${this.formattedTime}`
        ]
        alert(msg.join('\n'))
        this.generatePuzzle()
      }
    },
    isCellInvalid(row,col,value){
      if(!value){
        return true
      }
      for(let c = 0; c < 9 ; c++){
        if(this.puzzle[row][c].value === value && c!=col){
          return true
        }
      }
       for(let r = 0; r < 9 ; r++){
        if(this.puzzle[r][col].value === value && r!=row){
          return true
        }
      }
      const rowStart = Math.floor(row / 3) * 3
      const colStart = Math.floor(col / 3) * 3
      for(let r= rowStart; r < rowStart +3; r++){
      for(let c= colStart; c < colStart +3; c++){
      if(this.puzzle[r][c].value === value && !(r === row && c === col)){
          return true
        }
       } 
      }
      return false
    },
    isGameComplete(){
      for(let r = 0; r < 9; r++ ){
      for(let c = 0; c < 9; c++ ){
      if(this.isCellInvalid(r,c,this.puzzle[r][c].value)){
        return false
      }
      }
      }
      return true
    }
  }
};
</script>
<style scoped>
.sudoku {
  width: 100%;
  max-width: 420px;
  margin: 0.5rem auto;
  font-family: Arial, Helvetica, sans-serif;
}
.row {
  display: flex;
  align-items: center;
  justify-content: space-between;
}
.grid{
  width: calc(9 * 46.7px);
  margin: 0.5rem auto 1 rem;
}
.cell {
  display: block;
  width: 40px;
  height: 40px;
  box-sizing: border-box;
  border: 1px solid rgba(105, 3, 28, 0.63);
  font-size: 24px;
  line-height: 40px;
  text-align: center;
  cursor: default;
}
.cell.border-right {
  border-right-width: 3.5px;
  border-right-color: rgba(128, 1, 22, 0.596);
}
.cell.border-bottom {
  border-bottom-width: 3.5px;
  border-bottom-color: rgba(128, 1, 22, 0.596);
}
.box{
    box-shadow: 10px 10px 8px #7a011b23;
}
.cell.original{font-weight: bold;}
.cell:not(original){cursor: pointer;}
.cell.active{
  background-color: rgba(221, 36, 67, 0.685) !important;
  color: #fff;
}
.cell.invalid{
  background-color: rgba(247, 14, 14, 0.733);
  color: #fff;
}
.btn{
  width: 38px;
  height: 38px;
  font-size: 24px;
  cursor: pointer;
  margin-top: 1em;
}
.btn:disabled{
  cursor: not-allowed;
}
</style>
