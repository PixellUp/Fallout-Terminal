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
            <!-- class is added for the very first character on page load -->
            <span><span v-for="(i, letterIndex) in lines[n - 1]" ref="character" :class="{ 'selected': rowIndex === 0 && letterIndex === 0 }" :key="i.id">{{ i }}</span></span>
          </span>
        </div>
        <div class="terminal-middle">
          <span class="hex" v-for="n in numberOfRows" :key="n.id">0xF000&nbsp;
            <span><span v-for="i in lines[n + numberOfRows - 1]" ref="character" :key="i.id">{{ i }}</span></span>
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
const openingBrackets = ['<', '(', '[', '{'];
const closingBrackets = ['>', ')', ']', '}'];

export default {
  name: 'Terminal',

  data() {
    return {
      lines: [],
      attempts: 4,
      numberOfRows: 17
    };
  },

  methods: {

    printLines: function(rawArray, lineLength) {
      let self = this;

      let lines = [];
      console.log(lines);
      // split long array in to seperate arrays of characters for each line
      for (let i = 0; i < rawArray.length; i += lineLength) {
        lines.push(rawArray.slice(i, i + lineLength));
      }
      // push our array of arrays (lines) to data to be rendered in the template
      for (let i = 0; i < lines.length; ++i) {
        self.lines.push(lines[i].join(''));
      }
    },

    formatChars: function(charsString, numberOfChars, difficulty) {
      for (let i = 0; i < words.length; ++i) {
        // get a random index for somewhere to randomly place the word
        const randomIndex = Math.floor(Math.random() * numberOfChars);
        // get the ascii code of the character at this index
        const charCode = charsString[randomIndex].charCodeAt(0);
        console.log(randomIndex + difficulty);
        // check if it goes off the end
        if (randomIndex + difficulty > numberOfChars) {
          // remove 1 from i to repeat this iteration of the words and try again
          i -= 1;
          continue;
        }
        // check if our random index is a letter i.e. has already been replaced
        if (charCode >= 65 && charCode <= 90) {
          i -= 1;
          continue;
        // check ahead of the index so we don't overlap another word
        } else if (charsString[randomIndex + (difficulty)].charCodeAt(0) >= 65 &&
          charsString[randomIndex + (difficulty)].charCodeAt(0) <= 90) {
          i -= 1;
          continue;
        // also check behind so we don't overlap
        // randomIndex - 1 ensures a 1 character space so two words are not directly next to each other
        } else if (charsString[randomIndex - 1].charCodeAt(0) >= 65 &&
          charsString[randomIndex - 1].charCodeAt(0) <= 90 &&
          charsString[randomIndex - 1] < 0) {
          i -= 1;
          continue;
        } else if (charsString[randomIndex - 1] !== undefined || charsString[randomIndex + (difficulty + 1)] !== undefined) {
          let indexToReplace = randomIndex;
          const currentWord = words[i].split('');
          // we have split our word in to letters, now replace the indices starting at the random
          // index with the letters from our word
          for (let letter = 0; letter < currentWord.length; letter++) {
            charsString[indexToReplace] = currentWord[letter].toUpperCase();
            indexToReplace += 1;
          }
        }
      }
      // pass our entire string with words in and the length of each line to this
      // function to print them to the screen
      this.printLines(charsString, lineLength);
      // as well as sending the long string to the print lines function, we also save
      // it in to a variable defined at the top of the code to use further down in
      // our keypress logic
      completeString = charsString;
    },

    specialChars: function(numberOfChars) {
      let specialChars = [];
      let specialCharsString = [];
      // get all special characters using ascii codes
      for (let i = 33; i <= 126; ++i) {
        if (i < 48 || (i > 57 && i <= 63) || (i > 90 && i <= 95) || (i > 122 && i <= 126)) {
          specialChars.push(String.fromCharCode(i));
        }
      }
      // get random special chars and add them to the array
      for (let i = 0; i < numberOfChars; ++i) {
        specialCharsString.push(specialChars[Math.floor(Math.random() * specialChars.length)]);
      }
      // send the array to this function to add the words in
      // we pass in the array itself, the total length of the array
      // of special characters, and the difficulty which equals
      // the length of our words
      this.formatChars(specialCharsString, numberOfChars, difficulty);
    },

    changeSelection: function(newIndex, direction) {
      // get the span elements of the rendered characters
      let renderedChars = this.$refs.character;

      for (let i = 0; i < numberOfChars; ++i) {
        // remove currently selected
        if (renderedChars[i].classList.contains('selected')) {
          renderedChars[i].classList.remove('selected');
        }
      }
      renderedChars[newIndex].classList.add('selected');

      // if we are on an opening bracket, check ahead for a closing bracket and highlight them all
      if (openingBrackets.includes(renderedChars[newIndex].innerHTML)) {
        const bracketIndex = openingBrackets.indexOf(renderedChars[newIndex].innerHTML);
        const closingBracket = closingBrackets[bracketIndex];
        // check only on the current line
        // if a matching bracket is found, highlight everything between
        for (let i = newIndex; (i + 1) % 12 !== 0; ++i) {
          if (renderedChars[i].innerHTML === closingBracket) {
            for (let j = newIndex; j <= i; ++j) {
              renderedChars[j].classList.add('selected');
            }
          }
        }
      }

      // if we are on a letter, highlight the whole word
      if (renderedChars[newIndex].innerHTML.charCodeAt(0) >= 65 && renderedChars[newIndex].innerHTML.charCodeAt(0) <= 90) {
        switch (direction) {
          // if we move right in to a word, look ahead at the characters and highlight them all
          case 'right':
            for (let i = newIndex; i < (newIndex + 7); ++i) {
              renderedChars[i].classList.add('selected');
            }
            break;

          // if we move left in to a word, look behind at the characters and highlight them all
          case 'left':
            for (let i = newIndex; i > (newIndex - 7); --i) {
              renderedChars[i].classList.add('selected');
            }
            break;
        }
      }
    },

    keyboardInput: function() {
      let self = this;
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
            // if at the start, go nowhere
            if (selectedIndex === 0) {
              break;
            }
            // if currently selected index is part of a word, move to the end of the word
            if (completeString[selectedIndex].charCodeAt(0) >= 65 && completeString[selectedIndex].charCodeAt(0) <= 90) {
              let isLetter = true;
              while (isLetter) {
                selectedIndex -= 1;
                if (completeString[selectedIndex].charCodeAt(0) < 65 || completeString[selectedIndex].charCodeAt(0) > 90) {
                  selectedIndex += 1; // undo the original increase if it's no longer a word
                  isLetter = false;
                }
              }
            }
            // check if at start of row, if yes move to adjacent row
            if (selectedIndex >= (numberOfChars / 2) && (selectedIndex) % 12 === 0) {
              selectedIndex -= ((numberOfChars / 2) - 11);
            } else {
              // if no, move one to the right
              selectedIndex -= 1;
            }
            // console.log(selectedIndex);
            // console.log(completeString[selectedIndex]);
            self.changeSelection(selectedIndex, 'left');
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
              selectedIndex += ((numberOfChars / 2) - 11);
            } else {
              // if no, move one to the right
              selectedIndex += 1;
            }
            // console.log(selectedIndex);
            // console.log(completeString[selectedIndex]);
            self.changeSelection(selectedIndex, 'right');
            break;

          case 13: // enter
            console.log('enter');
            break;
        }
      });
    }

  },

  created: function() {
    console.clear();
    this.specialChars(numberOfChars); // creates characters/words and renders the screen
    this.keyboardInput(); // listens for keyboard input and perform operations
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
