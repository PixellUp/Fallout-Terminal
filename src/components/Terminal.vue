<template>
  <div class="terminal">

    <div class="screen-container">

      <div class="heading">
        <p>ROBCO INDUSTRIES (TM) TERMLINK PROTOCOL</p>
        <p>ENTER PASSWORD NOW</p>
        <br>
        <p><span class="attempts-count">{{ attempts }}</span> ATTEMPT(S) LEFT: <span class="attempts-squares"><span v-for="n in attempts" :key="n.id" class="attempts-square"></span></span></p>
        <br>
      </div>

      <div class="terminal-body">
        <div class="terminal-left">
          <span class="hex" v-for="(n, rowIndex) in numberOfRows" :key="n.id">0xF000&nbsp;
            <span><span v-for="(i, letterIndex) in lines[n - 1]" :class="{ 'selected': rowIndex === 0 && letterIndex === 0 }" :key="i.id">{{ i }}</span></span>
          </span>
        </div>
        <div class="terminal-middle">
          <span class="hex" v-for="n in numberOfRows" :key="n.id">0xF000&nbsp;
            <span><span v-for="i in lines[n + numberOfRows - 1]" :key="i.id">{{ i }}</span></span>
          </span>
        </div>
        <div class="terminal-right">
          <div class="input-area">
            <br>
            <span class="guess"></span>
            <br>
            <span class="entry-check"></span><br>
            <span class="correct"></span><br>
            ><span class="input"></span><span class="cursor"></span>
          </div>
        </div>

      </div>

    </div>

  </div>
</template>

<script>

const words = [
  'dangers',
  'sending',
  'central',
  'hunters',
  'resides',
  'believe',
  'venture',
  'pattern',
  'gangers',
  'mention',
  'cutters',
  'canteen',
  'cancers',
  'beliefs'
];
const numberOfChars = 408;
const difficulty = 7;
const lineLength = 12;
let completeString = '';

export default {
  name: 'Terminal',

  data() {
    return {
      lines: [],
      attempts: 4,
      isHighlighted: true,
      numberOfRows: 17
    };
  },

  methods: {

    printLines: function(rawArray, lineLength) {
      let self = this;
      let lines = [];
      console.log(lines);
      for (let i = 0; i < rawArray.length; i += lineLength) { // split long array in to seperate arrays of 12 characters
        lines.push(rawArray.slice(i, i + lineLength));
      }
      for (let i = 0; i < lines.length; ++i) { // push to data
        self.lines.push(lines[i].join(''));
      }
    },

    formatChars: function(charsString, stringLength, numberOfChars, difficulty) {
      for (let i = 0; i < words.length; ++i) {
        const randomIndex = Math.floor(Math.random() * numberOfChars);
        const charCode = charsString[randomIndex].charCodeAt(0);
        console.log(randomIndex + difficulty);
        if (randomIndex + difficulty > numberOfChars) {
          // remove 1 from i to repeat this iteration and try again
          i -= 1;
          continue;
        }
        // if our random character is a letter i.e. has already been replaced
        if (charCode >= 65 && charCode <= 90) {
          // remove 1 from i to repeat this iteration and try again
          i -= 1;
          continue;
        } else if (charsString[randomIndex + (difficulty)].charCodeAt(0) >= 65 &&
          charsString[randomIndex + (difficulty)].charCodeAt(0) <= 90) {
          // remove 1 from i to repeat this iteration and try again
          i -= 1;
          continue;
        } else if (charsString[randomIndex - 1].charCodeAt(0) >= 65 &&
          charsString[randomIndex - 1].charCodeAt(0) <= 90 &&
          charsString[randomIndex - 1] < 0) {
          // remove 1 from i to repeat this iteration and try again
          i -= 1;
          continue;
        } else if (charsString[randomIndex - 1] !== undefined || charsString[randomIndex + (difficulty + 1)] !== undefined) {
          let indexToReplace = randomIndex;
          const currentWord = words[i].split('');
          console.log(currentWord);
          for (let letter = 0; letter < currentWord.length; letter++) {
            charsString[indexToReplace] = currentWord[letter].toUpperCase();
            indexToReplace += 1;
          }
        }
      }
      this.printLines(charsString, lineLength);
      // as well as sending the long string to the print lines function, we also save
      // it in to a variable defined at the top of the code to use further down in
      // our keypress logic
      completeString = charsString;
    },

    specialChars: function(numberOfChars) {
      let specialChars = [];
      let specialCharsString = [];
      for (let i = 33; i <= 63; ++i) {
        if (i < 48 || i > 57) {
          specialChars.push(String.fromCharCode(i));
        }
      }
      for (let i = 0; i < numberOfChars; ++i) {
        specialCharsString.push(specialChars[Math.floor(Math.random() * specialChars.length)]);
      }
      this.formatChars(specialCharsString, 7, numberOfChars, difficulty);
    },

    highlight: function(index) {
      // const highlightTriggers = this.$refs;
      // console.log(highlightTriggers);
      // if (highlightTriggers[index - 1]) {
      //   highlightTriggers[index - 1].classList.remove('highlight');
      // }
      // highlightTriggers[index].classList.add('highlight');
    },

    attemptsLeft: function() {
      console.log('attemptsFunc');
      // attemptsSquares.innerHTML = '';
      // for (let i = 0; i < attempts; i++) {
      //   const square = document.createElement('span');
      //   square.classList.add('attempts-square');
      //   attemptsSquares.appendChild(square);
      // }
      // attemptsCount.innerHTML = parseFloat(attempts);
    },

    changeSelection: function(keyPress) {

    },

    keyboardInput: function() {
      let selectedIndex = 0;

      document.addEventListener('keydown', function(e) {
        switch (e.keyCode) {
          case 40: // down
            console.log('down');
            break;
          case 38: // up
            console.log('up');
            break;
          case 37: // left
            console.log('left');
            break;

          case 39: // right
            // if we're at the end of all the characters, GTFO
            if (selectedIndex === numberOfChars) {
              break;
            }
            // if currently selected index is part of a word, move to the end of the word
            if (completeString[selectedIndex].charCodeAt(0) >= 65 && completeString[selectedIndex].charCodeAt(0) <= 90) {
              let isLetter = true;
              while (isLetter) {
                selectedIndex += 1;
                if (completeString[selectedIndex].charCodeAt(0) < 65 || completeString[selectedIndex].charCodeAt(0) > 90) {
                  selectedIndex -= 1; // undo the original increase if it's no longer a word
                  isLetter = false;
                }
              }
            }
            // check if at end of row, if yes move to adjacent row
            if (selectedIndex < (numberOfChars / 2) && (selectedIndex + 1) % 12 === 0) {
              selectedIndex += (numberOfChars / 2);
            } else {
              // if no, move one to the right
              selectedIndex += 1;
            }
            break;
          case 13: // enter
            console.log('enter');
            break;
        }
      });
    }

  },

  created: function() {
    this.specialChars(numberOfChars); // render the screen
    this.keyboardInput(); // listen for keyboard input and perform operations
    this.attemptsLeft();
    this.highlight(1);
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss" scoped>
$lightgreen: #4afa8f;

.selected {
  background-color: $lightgreen;
  color: #0b2616
}
.attempts-squares {
  display: inline-block;
  .attempts-square {
    width: 0.7em;
    background: $lightgreen;
    height: 0.7em;
    display: inline-block;
    margin-right: 0.7em;
  }
}
.screen-container {
  height: 700px;
  width: 900px;
  margin: 0 auto;
  border: 1px solid black;
  position: relative;
  top: 10vh;
  padding: 20px;
}
.terminal-body {
  display: flex;
  flex-direction: row;
  .words { width: 70%; }
  .input-area { width: 30%; }
}
.terminal-left, .terminal-middle {
  display: flex;
  flex-direction: column;
  width: 35%;
}
.terminal-right {
  width: 30%;
  display: flex;
  flex-direction: column;
  justify-content: end;
}
</style>
