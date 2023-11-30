<template>
  <div class="simon_game">
    <h1>Simon Says</h1>
    <div class="board_controls">
      <div class="game-board">
        <div
          v-for="(button, index) in buttons"
          :key="index"
          class="game-button"
          :class="{ active: button.active }"
          :style="{ backgroundColor: button.color, ...button.style }"
          @click="handleButtonClick(index)"
        ></div>
      </div>
      <div class="game-controls">
        <h2>Round: {{ round }}</h2>
        <button @click="startGame" :disabled="sequencePlaying">
          Start Game
        </button>
        <div class="difficulty-controls">
          <h2>Difficulty level</h2>
          <label>
            <input
              type="radio"
              v-model="level"
              value="easy"
              :disabled="sequencePlaying || round > 0"
            />
            Easy
          </label>
          <label>
            <input
              type="radio"
              v-model="level"
              value="normal"
              :disabled="sequencePlaying || round > 0"
            />
            Normal
          </label>
          <label>
            <input
              type="radio"
              v-model="level"
              value="hard"
              :disabled="sequencePlaying || round > 0"
            />
            Hard
          </label>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      buttons: [
        {
          active: false,
          color: "blue",
          style: { backgroundColor: "dodgerblue" },
        },
        { active: false, color: "red", style: { backgroundColor: "#FF5643" } },
        {
          active: false,
          color: "yellow",
          style: { backgroundColor: "#FEEF33" },
        },
        {
          active: false,
          color: "green",
          style: { backgroundColor: "#BEDE15" },
        },
      ],
      difficultyLevels: {
        easy: 1500,
        normal: 1000,
        hard: 400,
      },
      sequence: [],
      playerInput: [],
      level: "easy",
      round: 0,
      sequencePlaying: false,
      computerTurn: false,
    };
  },
  methods: {
    startGame() {
      this.sequence = [];
      this.playerInput = [];
      this.round = 0;
      this.sequencePlaying = false;
      this.computerTurn = false;

      if (
        this.level === "easy" ||
        this.level === "normal" ||
        this.level === "hard"
      ) {
        this.playTurn();
      } else {
        this.level = "easy";
        this.playTurn();
      }
    },
    playTurn(buttonIndex) {
      if (buttonIndex !== undefined) {
        this.playerInput.push(buttonIndex);
        this.checkSequence();
        return;
      }

      this.round++;

      this.sequence.push(Math.floor(Math.random() * this.buttons.length));
      this.sequencePlaying = true;
      this.playSequence();
    },

    playSequence() {
      let i = 0;
      const interval = setInterval(() => {
        this.highlightButton(this.sequence[i]);

        i++;
        if (i >= this.sequence.length) {
          clearInterval(interval);
          this.playerInput = [];
          this.sequencePlaying = false;
        }
      }, this.difficultyLevels[this.level]);
    },

    highlightButton(buttonIndex) {
      this.buttons[buttonIndex].active = true;
      this.playSound(buttonIndex);
      setTimeout(() => {
        this.buttons[buttonIndex].active = false;
      }, 500);
    },
    playSound(buttonIndex) {
      const audio = new Audio();
      audio.src = require(`@/assets/sounds/sounds_${buttonIndex}.mp3`);
      audio.play();
    },
    checkSequence() {
      const isCorrect = this.playerInput.every(
        (value, index) => value === this.sequence[index]
      );

      console.log("Player Input:", this.playerInput);
      console.log("Generated Sequence:", this.sequence);

      if (!isCorrect) {
        this.gameOver();
        return;
      }

      if (this.playerInput.length === this.sequence.length) {
        this.playTurn();
      }
    },

    gameOver() {
      alert(`Game Over! Your score is: ${this.round - 1}`);
      this.round = 0;
    },
    handleButtonClick(index) {
      if (!this.sequencePlaying) {
        this.playSound(index);
        this.highlightButton(index);
        this.playTurn(index);
      }
    },
  },
};
</script>

<style scoped>
.simon_game {
  display: flex;
  flex-direction: column;
  align-items: center;
}
.board_controls {
  display: flex;
  margin-top: 50px;
}
.game-board {
  display: flex;
  width: 440px;
  height: 440px;
  flex-wrap: wrap;
  margin-right: 100px;
}
.game-button {
  display: flex;
  width: 220px;
  height: 220px;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  transition: background-color 0.2s, transform 0.2s;
}
.game-button:nth-child(1) {
  /* color: blue; */
  border-top-left-radius: 100%;
}
.game-button:nth-child(2) {
  /* color: red; */
  border-top-right-radius: 100%;
}
.game-button:nth-child(3) {
  /* color: yellow; */
  border-bottom-left-radius: 100%;
}
.game-button:nth-child(4) {
  /* color: green; */
  border-bottom-right-radius: 100%;
}
.active {
  transform: scale(1.1);
}
.game-controls {
  margin-top: 10px;
}

button {
  padding: 10px 20px;
  font-size: 16px;
}
.difficulty-controls {
  display: flex;
  flex-direction: column;
  margin-top: 30px;
}
.difficulty-controls label {
  margin-bottom: 10px;
}
</style>
