<template>
<div id="app">
    <section class="row">
        <div class="small-6 columns">
            <h1 class="text-center">YOU</h1>
            <div class="healthbar">
                <div class="healthbar text-center" style="background-color: green; margin: 0; color: white;" :style="{width: playerHealth + '%'}">
                    {{ playerHealth }}
                </div>
            </div>
        </div>
        <div class="small-6 columns">
            <h1 class="text-center">MONSTER</h1>
            <div class="healthbar">
                <div class="healthbar text-center" style="background-color: green; margin: 0; color: white;" :style="{width: monsterHealth + '%'}">
                    {{ monsterHealth }}
                </div>
            </div>
        </div>
    </section>
    <section class="row controls" v-if="!gameIsRunning">
        <div class="small-12 columns">
            <button id="start-game" @click="startGame">START NEW GAME</button>
        </div>
    </section>
    <section class="row controls" v-else>
        <div class="small-12 columns">
            <button id="attack" @click="attack">ATTACK</button>
            <button id="special-attack" @click="specialAttack">SPECIAL ATTACK</button>
            <button id="heal" @click="heal">HEAL</button>
            <button id="give-up" @click="giveUp">GIVE UP</button>
        </div>
    </section>
    <section class="row log" v-if="turns.length > 0">
        <div class="small-12 columns">
            <ul>
                <li v-for="turn in turns" :key="turn" :class="{'player-turn': turn.isPlayer, 'monster-turn': !turn.isPlayer}">
                    {{turn.text}}
                </li>
            </ul>
        </div>
    </section>
</div>
</template>

<script>
export default {
  name: 'app',
  data() {
    return{
      playerHealth: 100,
      monsterHealth: 100,
      gameIsRunning: false,
      turns: []
    }
  },
  methods: {
      startGame: function(){
          this.gameIsRunning = true;
          this.playerHealth = 100;
          this.monsterHealth = 100;
          this.turns = []
      },
      attack: function(){
          var playerDamage = this.calculateDamage(3, 10)
          this.monsterHealth -= playerDamage
          this.turns.unshift({
              isPlayer: true,
              text: 'Player hits the Monster for ' + playerDamage + '!'
          })
          if(this.checkWin()){
              return
          }
          this.monsterAttack()
      },
      specialAttack: function(){
          var specialDamage = this.calculateDamage(10, 20)
          this.monsterHealth -= specialDamage
          this.turns.unshift({
            isPlayer: true,
            text: 'Player hits the Monster with a stunning strike for ' + specialDamage + '!'
          })
          if(this.checkWin()){
              return
          }
          this.monsterAttack()
      },
      heal: function(){
          if(this.playerHealth <= 90){
            this.playerHealth += 10
          }
          else{
              this.playerHealth = 100
          }
          this.turns.unshift({
            isPlayer: true,
            text: 'Player regains 10 health'
          })
          this.monsterAttack()
      },
      giveUp: function(){
          this.gameIsRunning = false
      },
      calculateDamage: function(min, max){
          return Math.max(Math.floor(Math.random() * max) + 1, min)

      },
      checkWin: function(){
        if (this.monsterHealth <= 0){
            if(confirm('You won! New game?')){
                this.startGame()
            }
            else{
                this.gameIsRunning = false
            }
            return true
          }
        else if(this.playerHealth <=0){
            if(confirm('You lose, try again?')){
                this.startGame()
            }
            else{
                this.gameIsRunning = false
            }
            return true
        }
        return false
      },
      monsterAttack: function(){
        var monsterDamage = this.calculateDamage(5, 12)
        this.playerHealth -= monsterDamage
        this.checkWin()
        this.turns.unshift({
            isPlayer: false,
            text: 'Monster hits the Player for ' + monsterDamage + '!'
          })
      }
  }
}
</script>