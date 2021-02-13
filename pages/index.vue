<template v-for="">
  <div>
    <v-stage :config="configKonva" class="position">
      <v-layer>
        <template v-for="line in lines">
          <v-line
            :key="line.points"
            :config="line"
            @click="turnColor(currentPlayer, line)"
            @touchstart="turnColor(currentPlayer, line)"
          />
        </template>
      </v-layer>
    </v-stage>
    <div class="position">
      <button @click="reset" @touchstart="reset">Reset</button>
    </div>
    <p class="position">{{ currentPlayer.color }}のターンです</p>
    <p style="font-size: 32px" class="position" v-if="result">
      {{ result }}の勝ちです!
    </p>
  </div>
</template>

<script>
import Lines from '~/assets/lines.json'
import Answers from '~/assets/answers.json'
export default {
  data() {
    return {
      lines: Lines,
      answers: Answers,
      currentPlayer: {
        color: 'red',
      },
      result: '',
      configKonva: {
        width: 360,
        height: 360,
      },
    }
  },
  methods: {
    turnColor(player, target) {
      if (target.stroke === 'red' || target.stroke === 'blue') {
        return
      }
      target.stroke = player.color
      if (this.judge(player)) {
        this.result = player.color
      }
      this.changePlayer()
    },
    changePlayer() {
      if (this.currentPlayer.color === 'red') {
        this.currentPlayer.color = 'blue'
      } else if (this.currentPlayer.color === 'blue') {
        this.currentPlayer.color = 'red'
      }
    },
    judge(player) {
      const playersLine = this.lines
        .filter((value) => value.stroke === player.color)
        .map((line) => line.id)
        .sort()
      const playersIds = JSON.stringify(
        Object.keys(playersLine)
          .map((key) => playersLine[key])
          .sort()
      )
      for (const value of this.answers) {
        if (JSON.stringify(value.sort()) === playersIds) {
          return true
        }
      }
      return false
    },
    reset() {
      this.currentPlayer.color = 'red'
      this.result = ''
      this.lines.map((line) => (line.stroke = 'black'))
    },
  },
}
</script>
<style>
.position {
  display: flex;
  justify-content: center;
  align-items: center;
}
</style>
