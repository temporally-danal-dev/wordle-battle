<template>
  <div>
    <div class="title-row">
      <div class="title-box" style="background-color: grey">T</div>
      <div class="title-box" style="background-color: green">U</div>
      <div class="title-box" style="background-color: grey">T</div>
      <div class="title-box" style="background-color: green">O</div>
      <div class="title-box" style="background-color: grey">R</div>
      <div class="title-box" style="background-color: yellow">I</div>
      <div class="title-box" style="background-color: yellow">A</div>
      <div class="title-box" style="background-color: grey">L</div>
    </div>
    <div id="game-board">
      <div class="header">
        <h2>ANSWERS</h2>
      </div>
    </div>
    <div id="keyboard-cont" style="margin-top: 30px">
      <div class="error-row" v-show="error">
        <strong>{{ errorMsg }}</strong>
      </div>
      <div class="first-row">
        <button class="keyboard-button" @click="onClick">q</button>
        <button class="keyboard-button" @click="onClick">w</button>
        <button class="keyboard-button" @click="onClick">e</button>
        <button class="keyboard-button" @click="onClick">r</button>
        <button class="keyboard-button" @click="onClick">t</button>
        <button class="keyboard-button" @click="onClick">y</button>
        <button class="keyboard-button" @click="onClick">u</button>
        <button class="keyboard-button" @click="onClick">i</button>
        <button class="keyboard-button" @click="onClick">o</button>
        <button class="keyboard-button" @click="onClick">p</button>
      </div>
      <div class="second-row">
        <button class="keyboard-button" @click="onClick">a</button>
        <button class="keyboard-button" @click="onClick">s</button>
        <button class="keyboard-button" @click="onClick">d</button>
        <button class="keyboard-button" @click="onClick">f</button>
        <button class="keyboard-button" @click="onClick">g</button>
        <button class="keyboard-button" @click="onClick">h</button>
        <button class="keyboard-button" @click="onClick">j</button>
        <button class="keyboard-button" @click="onClick">k</button>
        <button class="keyboard-button" @click="onClick">l</button>
      </div>
      <div class="third-row">
        <button class="keyboard-button" @click="onClick">Del</button>
        <button class="keyboard-button" @click="onClick">z</button>
        <button class="keyboard-button" @click="onClick">x</button>
        <button class="keyboard-button" @click="onClick">c</button>
        <button class="keyboard-button" @click="onClick">v</button>
        <button class="keyboard-button" @click="onClick">b</button>
        <button class="keyboard-button" @click="onClick">n</button>
        <button class="keyboard-button" @click="onClick">m</button>
        <button class="keyboard-button" @click="onClick">Enter</button>
      </div>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
export default {
  name: "Tutorial",
  data() {
    return {
      answer: "abcde",
      cGuess: [],
      nLetter: 0,
      gCount: 0,
      errorMsg: "",
      error: false,
    };
  },
  methods: {
    insertLetter(pressedKey) {
      if (this.nLetter === 5) {
        return;
      }
      pressedKey = pressedKey.toLowerCase();

      let row = document.getElementsByClassName("letter-row")[this.gCount];
      if (row) {
        let box = row.children[this.nLetter];
        box.textContent = pressedKey;
        box.classList.add("filled-box");
        this.cGuess.push(pressedKey);
        this.nLetter += 1;
      }
    },
    deleteLetter() {
      let row = document.getElementsByClassName("letter-row")[this.gCount];
      let box = row.children[this.nLetter - 1];
      box.textContent = "";
      box.classList.remove("filled-box");
      this.cGuess.pop();
      this.nLetter -= 1;
    },
    checkGuess() {
      let row = document.getElementsByClassName("letter-row")[this.gCount];
      let guessString = "";
      let rightGuess = Array.from(this.answer);

      for (const val of this.cGuess) {
        guessString += val;
      }

      if (guessString.length !== 5) {
        this.error = true;
        this.errorMsg = "Not enough letters!";
        return;
      }

      this.insertInput();

      for (let i = 0; i < 6; i++) {
        if (i < 5) {
          let letterColor = "";
          let box = row.children[i];
          let letter = this.cGuess[i];

          let letterPosition = rightGuess.indexOf(this.cGuess[i]);
          if (letterPosition === -1) {
            letterColor = "grey";
          } else {
            if (this.cGuess[i] === rightGuess[i]) {
              letterColor = "green";
            } else {
              letterColor = "yellow";
            }
          }
          let delay = 250 * i;
          setTimeout(() => {
            box.style.backgroundColor = letterColor;
            this.shadeKeyBoard(letter, letterColor);
          }, delay);
        } else {
          let delay = 250 * i;
          setTimeout(() => {
            if (guessString === this.answer) {
              alert("You guessed right! Game over!");
              this.gCount = 0;
              this.$router.push({ name: "Home" });
            }
          }, delay);
        }
      }
      this.gCount += 1;
      this.cGuess = [];
      this.nLetter = 0;
    },
    shadeKeyBoard(letter, color) {
      for (const elem of document.getElementsByClassName("keyboard-button")) {
        if (elem.textContent === letter) {
          let oldColor = elem.style.backgroundColor;
          if (oldColor === "green") {
            return;
          }
          if (oldColor === "yellow" && color !== "green") {
            return;
          }
          elem.style.backgroundColor = color;
          break;
        }
      }
    },
    onClick(e) {
      let pressedKey = String(e.target.textContent);
      this.error = false;
      this.errorMsg = "";
      if (pressedKey === "Del" && this.nLetter !== 0) {
        this.deleteLetter();
        return;
      }

      if (pressedKey === "Enter") {
        this.checkGuess();
        return;
      }

      if (
        pressedKey.length === 1 &&
        pressedKey.charCodeAt(0) > 96 &&
        pressedKey.charCodeAt(0) < 123
      ) {
        this.insertLetter(pressedKey);
      } else {
        return;
      }
    },
    insertInput() {
      let board = document.getElementById("game-board");
      let row = document.createElement("div");
      row.className = "letter-row";

      for (let j = 0; j < 5; j++) {
        let box = document.createElement("div");
        box.className = "letter-box";
        row.appendChild(box);
      }

      board.appendChild(row);
    },
    onKeyup(e) {
      this.error = false;
      this.errorMsg = "";
      let pressedKey = String(e.key);
      if (pressedKey === "Backspace" && this.nLetter !== 0) {
        this.deleteLetter();
        return;
      }

      if (pressedKey === "Enter") {
        this.checkGuess();
        return;
      }

      if (
        pressedKey.length === 1 &&
        pressedKey.charCodeAt(0) > 96 &&
        pressedKey.charCodeAt(0) < 123
      ) {
        this.insertLetter(pressedKey);
      }
      if (
        pressedKey.length === 1 &&
        pressedKey.charCodeAt(0) > 64 &&
        pressedKey.charCodeAt(0) < 91
      ) {
        this.insertLetter(pressedKey);
      }
    },
  },
  mounted() {
    this.insertInput();
    document.addEventListener("keyup", this.onKeyup);
  },
  beforeDestroy() {
    document.removeEventListener("keyup", this.onKeyup);
  },
};
</script>
<style>
h1 {
  text-align: center;
}

.letter-box {
  border: 2px solid gray;
  border-radius: 3px;
  margin: 2px;
  font-size: 2.5rem;
  font-weight: 700;
  height: 3rem;
  width: 3rem;
  display: flex;
  justify-content: center;
  align-items: center;
  text-transform: uppercase;
}

.filled-box {
  border: 2px solid black;
}

.letter-row {
  display: flex;
}

.title-row {
  display: flex;
  justify-content: center;
}

.title-box {
  border: 2px solid gray;
  border-radius: 3px;
  margin: 2px;
  font-size: 2.5rem;
  font-weight: 700;
  height: 3rem;
  width: 3rem;
  display: flex;
  justify-content: center;
  align-items: center;
  text-transform: uppercase;
}

#keyboard-cont {
  margin: 1rem 0;
  display: flex;
  flex-direction: column;
  align-items: center;
}

#keyboard-cont div {
  display: flex;
}

.second-row {
  margin: 0.5rem 0;
}

.keyboard-button {
  font-size: 1rem;
  font-weight: 700;
  padding: 0.5rem;
  margin: 0 2px;
  cursor: pointer;
  text-transform: uppercase;
}

.error-row {
  margin: 0.5rem 0;
  color: red;
}
</style>
