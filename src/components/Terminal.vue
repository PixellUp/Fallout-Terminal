<template>
  <div class="terminal">

    <div class="screen-container">

      <div class="scan-lines"></div>

      <!-- background effect markup -->
      <div class="terminal-window">
        <div class="terminal-layer"></div>
        <div class="terminal-overlay"></div>
      </div>

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
          <div class="output-area">
            <p v-for="output in outputText" :key="output.id">{{ output }}</p>
          </div>
          <div class="input-area">
            ><span class="input"><span>{{ currentlySelected }}</span></span><span class="cursor"></span>
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
const password = words[Math.floor(Math.random() * words.length)].toUpperCase();
let usedBrackets = [];

export default {
  name: 'Terminal',

  data() {
    return {
      lines: [],
      attempts: 4,
      numberOfRows: 17,
      currentlySelected: '',
      outputText: []
    };
  },

  methods: {

    printLetters: function(letters) {
      // prints out characters one at a time on the screen
      let self = this;

      self.currentlySelected = '';
      let currentLetters = [];
      letters.forEach((letter, index) => { // print letters one at a time
        setTimeout(() => {
          currentLetters.push(letter);
          self.currentlySelected = currentLetters.join('');
        }, 30 * (index + 1));
      });
    },

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
      let self = this;

      for (let i = 0; i < words.length; ++i) {
        // get a random index for somewhere to randomly place the word
        const randomIndex = Math.floor(Math.random() * numberOfChars);
        // prevent 0 from being chosen as this causes problems later on with randomIndex - 1
        if (randomIndex === 0) {
          // remove 1 from i to repeat this iteration of the words and try again
          i -= 1;
          continue;
        }
        // get the ascii code of the character at this index
        const charCode = charsString[randomIndex].charCodeAt(0);
        console.log(randomIndex + difficulty);
        // check if it goes off the end
        if (randomIndex + (difficulty + 1) >= numberOfChars) {
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
          charsString[randomIndex - 1].charCodeAt(0) <= 90) {
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
      self.printLines(charsString, lineLength);
      // as well as sending the long string to the print lines function, we also save
      // it in to a variable defined at the top of the code to use further down in
      // our keypress logic
      completeString = charsString;
    },

    specialChars: function(numberOfChars) {
      let self = this;

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
      self.formatChars(specialCharsString, numberOfChars, difficulty);
    },

    showCurrentlySelected: function(renderedChars) {
      let self = this;

      // add all currently selected characters to data
      let currentSelection = [];
      for (let i = 0; i < numberOfChars; ++i) {
        if (renderedChars[i].classList.contains('selected')) {
          currentSelection.push(renderedChars[i].innerText);
        }
      }
      self.printLetters(currentSelection);
    },

    changeSelection: function(newIndex) {
      let self = this;

      // get the span elements of the rendered characters
      let renderedChars = self.$refs.character;

      for (let i = 0; i < numberOfChars; ++i) {
        // remove currently selected
        if (renderedChars[i].classList.contains('selected')) {
          renderedChars[i].classList.remove('selected');
        }
      }
      renderedChars[newIndex].classList.add('selected');

      // if we are on an opening bracket, check ahead for a closing bracket and highlight them all
      if (openingBrackets.includes(renderedChars[newIndex].innerText)) {
        const bracketIndex = openingBrackets.indexOf(renderedChars[newIndex].innerText);
        const closingBracket = closingBrackets[bracketIndex];
        // check only on the current line
        // if a matching bracket is found, highlight everything between
        // highlight the first one, then check after that starting with newIndex + 1
        renderedChars[newIndex].classList.add('selected');
        for (let i = newIndex + 1; i % 12 !== 0; ++i) {
          if (renderedChars[i].innerText === closingBracket) {
            for (let j = newIndex; j <= i; ++j) {
              renderedChars[j].classList.add('selected');
            }
          }
        }
      }

      // if we are on a letter, highlight the whole word
      if (renderedChars[newIndex].innerHTML.charCodeAt(0) >= 65 && renderedChars[newIndex].innerHTML.charCodeAt(0) <= 90) {
        // if we move in to a word, look around and highlight characters
        // look to the right
        for (let i = newIndex; i < (newIndex + 7); ++i) {
          if (renderedChars[i].innerHTML.charCodeAt(0) >= 65 && renderedChars[i].innerHTML.charCodeAt(0) <= 90) {
            renderedChars[i].classList.add('selected');
          } else {
            break;
          }
        }
        // look to the left
        for (let i = newIndex; i > (newIndex - 7); --i) {
          if (renderedChars[i].innerHTML.charCodeAt(0) >= 65 && renderedChars[i].innerHTML.charCodeAt(0) <= 90) {
            renderedChars[i].classList.add('selected');
          } else {
            break;
          }
        }
      }

      self.showCurrentlySelected(renderedChars);
    },

    removeDud: function(brackets) {
      let self = this;

      // we add this bracket pair to an array so that further down
      // we can make sure it is not usable more than once
      usedBrackets.push(brackets);
      let dudRemoved = false;
      // keep getting a new random index until we find one that is in a word
      while (!dudRemoved) {
        let randomIndex = Math.floor(Math.random() * (numberOfChars - 1));
        const characters = self.$refs.character;
        // if chosen index is part of a word
        if (characters[randomIndex].innerText.charCodeAt(0) >= 65 && characters[randomIndex].innerText.charCodeAt(0) <= 90) {
          // we have to check the letter before the randomly chosen one to see if
          // we are at the start of a word or in the middle of a word before we remove it.
          // if we are in the middle of a word reduce the index by 1 until we get to
          // the start of a word (startOfWord = true)
          let startOfWord = false;
          while (!startOfWord) {
            if (characters[randomIndex - 1].innerText.charCodeAt(0) >= 65 && characters[randomIndex - 1].innerText.charCodeAt(0) <= 90) {
              randomIndex -= 1;
            } else {
              startOfWord = true;
            }
          }
          // make sure our randomly found word is not the password as that isn't a dud
          let checkIfPassword = [];
          for (let i = randomIndex; i < randomIndex + 7; ++i) {
            checkIfPassword.push(characters[i].innerText);
          }
          if (checkIfPassword.join('') === password) {
            continue;
          } else {
            for (let i = randomIndex; i < randomIndex + 7; ++i) {
              characters[i].innerText = '.';
            }
            dudRemoved = true;
            self.outputText.push(`>${brackets}`);
            self.outputText.push(`>Dud removed.`);
            // if it is too tall, remove some text items from the top
            while (self.outputText.length > (self.numberOfRows - 2)) {
              self.outputText.shift();
            }
          }
        }
      }
    },

    correct: function(guess) {
      let self = this;

      //  create output text
      self.outputText.push(`>${guess}`);
      self.outputText.push(`>Exact match!`);
      self.outputText.push(`>Please wait`);
      self.outputText.push(`>while system`);
      self.outputText.push(`>is accessed`);
      // if it is too tall, remove some text items from the top
      while (self.outputText.length > (self.numberOfRows - 2)) {
        self.outputText.shift();
      }
    },

    incorrect: function(guess, likeness, isWord = true) {
      let self = this;

      if (isWord) {
        // reduce attempts by 1 and create output text
        self.attempts -= 1;
        self.outputText.push(`>${guess}`);
        self.outputText.push(`>Entry denied`);
        self.outputText.push(`>${likeness}/${difficulty} correct.`);
      } else {
        // if we pass in a single random character
        self.outputText.push(`>${guess}`);
        self.outputText.push(`>Invalid input`);
      }
      // if output text is too tall, remove some text items from the top
      while (self.outputText.length > (self.numberOfRows - 2)) {
        self.outputText.shift();
      }
    },

    checkGuess: function(guess) {
      let self = this;

      // check likeness of guess to password
      let likeness = 0;
      for (let i = 0; i < guess.length; ++i) {
        if (guess[i] === password[i]) {
          likeness += 1;
        } else {
          continue;
        }
      }
      if (likeness === difficulty) {
        self.correct(guess);
      } else {
        self.incorrect(guess, likeness);
      }
    },

    keyboardInput: function() {
      let self = this;
      let selectedIndex = 0;

      document.addEventListener('keydown', function(e) {
        switch (e.keyCode) {
          case 40: // down
            if (selectedIndex >= (numberOfChars - 11) || (selectedIndex >= (numberOfChars / 2 - 12) && selectedIndex <= (numberOfChars / 2 - 1))) {
              break;
            }
            selectedIndex += 12;

            self.changeSelection(selectedIndex);
            break;

          case 38: // up
            // if on the top rows, go nowhere
            if (selectedIndex < 12 || (selectedIndex >= (numberOfChars / 2) && selectedIndex <= (numberOfChars / 2 + 11))) {
              break;
            }
            selectedIndex -= 12;

            self.changeSelection(selectedIndex);
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
            self.changeSelection(selectedIndex);
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
            self.changeSelection(selectedIndex);
            break;

          case 13: // enter
            const guess = self.currentlySelected;
            console.log(guess);
            // if it is not a word or bracket pair
            if (guess.length === 1) {
              self.incorrect(guess, 0, false);
            }
            // if guess is a bracket pair
            if (guess.length > 1 && !guess.match(/[A-Z]/) && !usedBrackets.includes(guess)) {
              self.removeDud(guess);
            }
            // if guess is a word
            if (guess.length > 1 && guess.match(/[A-Z]/)) {
              self.checkGuess(guess);
            }
            break;
        }
      });
    }

  },

  created: function() {
    console.clear();
    this.specialChars(numberOfChars); // creates characters/words and renders the screen
    this.keyboardInput(); // listens for keyboard input and perform operations
    console.log(password);
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss" scoped>
$lightgreen: #4afa8f;

// blinking cursor
@keyframes blinking {
  from, to { border-color: transparent; }
  50% { border-color: $lightgreen; }
}
.cursor {
  box-sizing: border-box;
  border-left: .5em solid;
  animation: blinking 1s step-end infinite;
  height: 1em;
  width: 0;
}

// terminal pulse effect
@keyframes vline {
  0% { top: 0px;}
  100% { top: 100%;}
}
.terminal-window {
  border-radius: 50px;
  width: 100%;
  height: 100%;
  margin: -20px 0 0 -20px;
  background-color: #212121;
  position: absolute;
  z-index: -9999;
  overflow: hidden;
  .terminal-layer {
    width: 100%;
    height: 100%;
    position: absolute;
    z-index: 4001;
    background: radial-gradient(ellipse at center, #002c12 0%, rgba(64, 64, 64, 0) 80%);
    opacity: .9;
  }
  .terminal-overlay {
    width: 100%;
    height: calc(100% + 300px);
    top: -150px;
    z-index: 4100;
    position: absolute;
    &:before {
      content : '';
      position : absolute;
      top : 0px;
      left: 0px;
      width : 100%;
      height : 150px;
      background : #fff;
      background: -moz-linear-gradient(top, rgba(30,87,153,0) 0%, rgba(80,80,80,0.8) 90%, rgba(221,221,221,0) 100%); /* FF3.6-15 */
      background: -webkit-linear-gradient(top, rgba(30,87,153,0) 0%,rgba(80,80,80,0.8) 90%,rgba(221,221,221,0) 100%); /* Chrome10-25,Safari5.1-6 */
      background: linear-gradient(to bottom, rgba(30,87,153,0) 0%,rgba(80,80,80,0.8) 90%,rgba(221,221,221,0) 100%); /* W3C, IE10+, FF16+, Chrome26+, Opera12+, Safari7+ */
      filter: progid:DXImageTransform.Microsoft.gradient( startColorstr='#001e5799', endColorstr='#00dddddd',GradientType=0 ); /* IE6-9 */
      opacity : .2;
      animation: vline 3s linear infinite;
    }
  }
}
.scan-lines {
  background: linear-gradient(#444 50%, #000 50%);
  background-size: 100% 4px;
  background-repeat: repeat-y;
  opacity: .2;
  box-shadow: inset 0px 0px 1px 1px rgba(0, 0, 0, .8);
  z-index: 999999;
  height: 100%;
  width: 100%;
  margin: -20px 0 0 -20px;
  border-radius: 50px;
  position: absolute;
}
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
  border-radius: 50px;
}
.terminal-body {
  display: flex;
  flex-direction: row;
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
.output-area { margin-bottom: 22px; }
</style>
