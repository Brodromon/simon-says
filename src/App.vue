<template>
  <div id="app">
    <h1>Simon the game</h1>
    <div class="game-wrapper">
      <ul ref='ul' v-if="!lose">
        <li @click="register(1)" class="round-btn red"></li>
        <li @click="register(2)" class="round-btn blue"></li>
        <li @click="register(3)" class="round-btn yellow"></li>
        <li @click="register(4)" class="round-btn green"></li>
      </ul>
      <div v-if="!lose" id="menu">
          <h3>{{round}}</h3>
          <button v-if="!round" @click="start()">start</button>
          <div id="game-modes">
            <label for="easy">Easy </label><input @click="changeMode('easy')" name="mode" checked type="radio">
            <label for="normal">Normal </label><input @click="changeMode('normal')" name="mode" type="radio">
            <label for="hard">Hard </label><input @click="changeMode('hard')" name="mode" type="radio">
          </div>
        </div>
      <div v-else id="lose-container">
        <h3>You lose!</h3>
        <button @click="lose = !lose">Home</button>
        <button @click="start()">Start again</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      seq: [],
      copy: [],
      round: 0,
      playable: false,
      active: true,
      lose: false,
      mode: 'easy'
    }
  },
  methods: {

    start() {
      if (!this.round) {
        this.seq = [],
        this.copy = [],
        this.round = 0,
        this.active = true,
        this.lose = false,
        this.mode = 'easy'
        this.newRound()
      }
    },
    
    newRound() {
      setTimeout(()=> {
        this.playable = true
        this.round++
        this.seq.push(this.randomNum())
        this.copy = this.seq.slice(0)
        this.animate(this.seq)
      }, 500)
    },

    register(e) {
      this.playSound(e)
      this.light(e)
      if(this.playable) {
        let desiredNum = this.copy.shift()
        let selectedNum = e
        this.active = (desiredNum === selectedNum)
        this.loseCheck()
      }
    },

    playSound(e) {
      let a = new Audio(require('./assets/' + e + '.mp3'));
      a.play();
    },

    light(e) {
      //console.log(this.$refs.ul.childNodes[e-1])
      this.$refs.ul.childNodes[e-1].classList.add('active')
      window.setTimeout(()=> {
        this.$refs.ul.childNodes[e-1].classList.remove('active')
      }, 300)
    },
  
    animate(seq) {
      let i = 0
      let time = 0
      if (this.mode == 'easy')
        time = 1500
      else if (this.mode == 'normal')
        time = 1000
      else if (this.mode == 'hard')
        time = 400
      let interval = setInterval(()=> {
        this.playSound(seq[i])
        this.light(seq[i])
        i++
        if (i >= seq.length) {
          clearInterval(interval)
        }
      }, time)
      
    },

    changeMode(mode) {
      this.mode = mode
      //console.log(this.mode)
    },

    randomNum() {
      return Math.floor((Math.random()*4)+1)
    },

    loseCheck() {
      if (this.copy.length === 0 && this.active) {
        this.playable = false
        this.newRound()
      }
      else if (!this.active) {
        this.playable = false
        this.endGame()
      }
    },

    endGame() {
      this.round = 0
      this.lose = true
    },
  }
}
</script>

<style lang="sass">
#app
  text-align: center
.game-wrapper
  width: 400px
  display: inline-block
  text-align: center
  #menu
    display: inline-block
    h3
      font-size: 35px
      margin: 10px 0
      padding: 0
      display: block
    button
      border: 2px #44739c solid
      background: #63b0f2
      padding: 7px 15px
      color: #fff
      font-weight: bold
      text-transform: uppercase
      border-radius: 10px
      font-size: 20px
      &:hover
        background: #518ec2
      &:active
        background: #396285
        border-color: #396285
  ul
    width: 300px
    height: 300px
    list-style: none
    margin: 0
    padding: 0
    position: relative
    li 
      display: inline-block
      height: 300px
      width: 300px
      border-radius: 150px
      position: absolute
      left: 50px
      border: 1px #333 solid
      box-sizing: border-box
      filter: brightness(95%)
      &:hover
        filter: brightness(100%)
      &:active
        filter: brightness(120%)
    .red
      background: #ed2b3b
      clip: rect(0, 150px, 150px, 0)
    .blue
      background: #2b62ed
      clip: rect(0, 300px, 150px, 150px) 
    .yellow
      background: #ede72b
      clip: rect(150px, 150px, 300px, 0)  
    .green
      background: #2bed4b
      clip: rect(150px, 300px, 300px, 150px)
    .active
      filter: brightness(120%)  
</style>