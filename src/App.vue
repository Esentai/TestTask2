<template>
  <div id="app">
    <div>
      <div class="game fullSize">
        <div class="square" @click="clicked(1)" :class="{lit: isLit[1]}"></div>
        <div class="square" @click="clicked(2)" :class="{lit: isLit[2]}"></div>
        <div class="square" @click="clicked(3)" :class="{lit: isLit[3]}"></div>
        <div class="square" @click="clicked(4)" :class="{lit: isLit[4]}"></div>
        <div class="start" @click="startGame">{{ centerButton }}
          <span v-show="playing">{{ showScore }}</span>
        </div>
      </div>
      <div class="complexityBtns">
        <button :class="{'active': complexity === 1500}" @click="changeComplexity(1500)">Легкий</button>
        <button :class="{'active': complexity === 1000}" @click="changeComplexity(1000)">Средний</button>
        <button :class="{'active': complexity === 800}" @click="changeComplexity(800)">Сложный</button>
      </div>
    </div>
  </div>
</template>

<script>

export default {
  name: 'App',
  data: () => {
    return {
      complexity: 1500,
      centerButton: "СТАРТ",
      playing: false,
      isClickable: false,
      isWon: false,
      isWrong: false,
      score: 0,
      sequence: [],
      sequenceInterval: null,
      playerSequence: [],
      sounds: [new Audio('assets/sounds/1.mp3'), new Audio('assets/sounds/2.mp3'),new Audio('assets/sounds/3.mp3'),new Audio('assets/sounds/4.mp3')],
      isLit: {
        1: false,
        2: false,
        3: false,
        4: false
      }
    }
  },
  computed: {
    showScore() {
      if (this.isWon) {
        return "Play Again?";
      }
      return "Score: " + this.score;
    }
  },
  methods: {
    startGame() {
      this.playing = true;
      this.sequence = [];
      this.playerSequence = [];
      this.centerButton = "ЗАНОВО";
      this.isWon = false;
      this.isWrong = false;
      this.score = 0;
      clearInterval(this.sequenceInterval);
      this.showSequence();
    },
    changeComplexity(speed){
      this.complexity = speed;
    },
    clicked(tile) {
      if (this.isClickable) {
        this.playSound(tile);
        this.lightUp(tile);
        this.playerSequence.push(tile);
        this.checkWinLose();
      }
    },
    checkWinLose() {
      for (let i = 0; i < this.playerSequence.length; i++) {
        if (this.playerSequence[i] !== this.sequence[i]) {
          if (this.strict) {
            this.centerButton = "Wrong!";
            this.isWrong = true;
            this.isClickable = false;
            return setTimeout(() => {
              this.startGame();
            }, 1000);
          }
          this.playerSequence = [];
          this.centerButton = "Wrong!";
          this.isWrong = true;
          setTimeout(() => {
            this.centerButton = "RESET";
            this.isWrong = false;
          }, 1000);
          this.showSequence(true);
        }
      }
      if (this.playerSequence.length === this.sequence.length) {
        this.playerSequence = [];
        this.score++;
        if (this.score === 20) {
          this.centerButton = "Winner!";
          this.isClickable = false;
          this.isWon = true;
        } else {
          this.showSequence();
        }
      }
    },
    lightUp(tile) {
      this.isLit[tile] = true;
      setTimeout(() => {
        this.isLit[tile] = false;
      }, 300);
    },
    playSound(tile) {
      console.log(tile)
      // this.sounds[tile - 1].play();
    },
    showSequence(redo) {
      let currentIndex = 0;
      let speed = this.sequence.length === 0 ? this.complexity : this.complexity-100;
      this.isClickable = false;
      if (!redo) {
        this.sequence.push(Math.floor(Math.random() * 4 + 1));
      }
      this.sequenceInterval = setInterval(() => {
        if (currentIndex >= this.sequence.length) {
          clearInterval(this.sequenceInterval);
          return (this.isClickable = true);
        }
        this.playSound(this.sequence[currentIndex]);
        this.lightUp(this.sequence[currentIndex]);
        currentIndex++;
      }, speed);
    }
  }
}
</script>

<style lang="scss">
  @import "assets/style.scss";
</style>
