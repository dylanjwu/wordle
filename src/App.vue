<template>
  <div id="app">
    <h2>Wordle</h2>
    <div class="game-grid">
      <div :style="{ 'background-color': highlight(letter)}" class="letter-square" v-for="letter in letters" :key="letter.index">
        {{  letter.value }}
      </div>
    </div>
  </div>
</template>

<script>

export default {
  name: 'App',
  created() {
    window.addEventListener('keyup', e => {
      const aToZ = 'abcdefghijklmnopqrstuvwxyz';
      const alphabet = aToZ.toUpperCase().split('').concat(aToZ.split(''));
      if (alphabet.includes(e.key)){
        const letter = e.key.toUpperCase();
        const currIndex = this.wordIndex*5 + this.index;
        this.$set(this.letters, currIndex, {...this.letters[currIndex], value: letter});

        if (this.index === 4) {
          for (let i = 0; i < 5; i++) {
            const val = this.letters[currIndex-i].value;
            this.$set(this.letters, currIndex-i, {...this.letters[currIndex-i], inWord: this.wordToGuess.includes(val), correctPlacement: this.wordToGuess[this.index-i] === val});
          }
          this.wordIndex++;
          this.index = 0;
        }
        else {
          this.index++;
        }
      }
      else if (e.key === 'Backspace' && !(this.index % 5 === 0) && this.index > 0) {
        const lastIndex = this.wordIndex*5+this.index-1;
        this.$set(this.letters, lastIndex, {...this.letters[lastIndex], value: '', inWord: false, correctPlacement: false});
        this.index--;
      }
      console.log(this.index);
    })
  },
  data() {
    return {
      wordToGuess: 'howls'.toUpperCase().split(''),
      index: 0,
      wordIndex: 0,
      letters: new Array(25).fill({}).map((__, i) => {
          return {index: i, value: '', inWord: false, correctPlacement: false};
      })
    }
  },
  methods: {
    highlight(letter) {
      if (letter.correctPlacement) return 'lightgreen';
      if (letter.inWord) return 'orange';
      return '';
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 20px;
}
.game-grid {
  display: flex;
  justify-content: center;
  padding-top: 10px;
  padding-bottom: 10px;
  border: 1px solid black;
  display: grid;
  grid-template-columns: repeat(5, 50px);
  grid-template-rows: repeat(5, 50px);
  grid-column-gap: 5px;
  grid-row-gap: 5px;
}

.letter-square {
  border: 1px solid black;
  display: flex;
  justify-content: center;
  align-items: center;
}
</style>
