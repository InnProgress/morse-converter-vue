<template>
  <div id="app">
    <textarea placeholder="Your text" v-model="text"></textarea>
    <button @click="convert">Convert</button>
    <button v-if="morseCode" @click="play">Play</button>
    <button v-if="morseCode" @click="stop">Stop</button>

    <div id="result">
      {{ morseCode }}
    </div>
  </div>
</template>

<script>

export default {
  data() {
    return {
      text: '',
      morseCode: '',
      morseValues: [
        {'symbol':'A', 'value':".-"},
        {'symbol':'B', 'value':"-..."},
        {'symbol':'C', 'value':"-.-."},
        {'symbol':'D', 'value':"-.."},
        {'symbol':'E', 'value':"."},
        {'symbol':'F', 'value':"..-."},
        {'symbol':'G', 'value':"--."},
        {'symbol':'H', 'value':"...."},
        {'symbol':'I', 'value':".."},
        {'symbol':'J', 'value':".---"},
        {'symbol':'K', 'value':"-.-"},
        {'symbol':'L', 'value':".-.."},
        {'symbol':'M', 'value':"--"},
        {'symbol':'N', 'value':"-."},
        {'symbol':'O', 'value':"---"},
        {'symbol':'P', 'value':".--."},
        {'symbol':'Q', 'value':"--.-"},
        {'symbol':'R', 'value':".-."},
        {'symbol':'S', 'value':"..."},
        {'symbol':'T', 'value':"-"},
        {'symbol':'U', 'value':"..-"},
        {'symbol':'V', 'value':"...-"},
        {'symbol':'W', 'value':".--"},
        {'symbol':'X', 'value':"-..-"},
        {'symbol':'Y', 'value':"-.--"},
        {'symbol':'Z', 'value':"--.."},
        {'symbol':'1', 'value':".----"},
        {'symbol':'2', 'value':"..---"},
        {'symbol':'3', 'value':"...--"},
        {'symbol':'4', 'value':"....-"},
        {'symbol':'5', 'value':"....."},
        {'symbol':'6', 'value':"-...."},
        {'symbol':'7', 'value':"--..."},
        {'symbol':'8', 'value':"---.."},
        {'symbol':'9', 'value':"----."},
        {'symbol':'0', 'value':"-----"},
        {'symbol':'.', 'value':".-.-.-"},
        {'symbol':',', 'value':"--..--"},
        {'symbol':':', 'value':"---..."},
        {'symbol':'?', 'value':"..--.."},
        {'symbol':'\'', 'value':".----."},
        {'symbol':'-', 'value':"-....-"},
        {'symbol':'/', 'value':"-..-."},
        {'symbol':'(', 'value':"-.--.-"},
        {'symbol':')', 'value':"-.--.-"},
        {'symbol':'"', 'value':".-..-."},
        {'symbol':'@', 'value':".--.-."},
        {'symbol':'=', 'value':"-...-"},
        {'symbol':' ', 'value':" "}
      ],
      oscillator: false
    }
  },
  methods: {
    convert() {
      this.morseCode = '';

      [...this.text].forEach(v => {
        this.morseCode += this.morseValues.find(value => value.symbol === v.toUpperCase()).value + ' ';
      });

    },
    play() {

      var AudioContext = window.AudioContext || window.webkitAudioContext;
      var ctx = new AudioContext();
      var dot = 1.2 / 15;


      var t = ctx.currentTime;


      this.stop();

      this.oscillator = ctx.createOscillator();
      this.oscillator.type = "sine";
      this.oscillator.frequency.value = 600;

      var gainNode = ctx.createGain();
      gainNode.gain.setValueAtTime(0, t);

      [...this.morseCode].forEach(symbol => {
        switch(symbol) {
            case '.':
                gainNode.gain.setValueAtTime(1, t);
                t += dot;
                gainNode.gain.setValueAtTime(0, t);
                t += dot;
                break;
            case '-':
                gainNode.gain.setValueAtTime(1, t);
                t += 3 * dot;
                gainNode.gain.setValueAtTime(0, t);
                t += dot;
                break;
            case ' ':
                t += 7 * dot;
                break;
        }
      });

      this.oscillator.connect(gainNode);
      gainNode.connect(ctx.destination);

      this.oscillator.start();

      return false;

    },
    stop() {
      if(this.oscillator) this.oscillator.stop();
    }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin: 80px auto;

  background-color: white;
  width: fit-content;
  padding: 30px;
}
body {
  background-image: url('https://timedotcom.files.wordpress.com/2016/07/mars-morse-code-wide.jpg');
  background-size: cover;
}
button {
  display: block;
  margin: 5px auto;
}
textarea {
  min-width: 130px;
  max-width: 90%;
  min-height: 30px;
}
#result {
  font-size: 22px;
  margin-top: 20px;
}
</style>
