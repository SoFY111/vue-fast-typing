<template>
    <div class="container jumbotron">
        <h1 class="display-4">Hızlı Yazma Yarışması</h1>
        <p class="lead">Ne kadar hızlı klavye kullandığını test et.</p>
        <div>
            Doğru Sayısı:{{trueCount}}<br>
            Yanlış Sayısı:{{falseCount}}
        </div>
        <hr class="my-4">
        <div v-if="isFinish" class="alert alert-primary">
          <h3>Oyun Bitti</h3>
          <p class="display-4 ">{{dks}} DKS</p> <br>
          <span>Doğruluk Yüzdesi: %{{truePercent}}</span> <br>
          <span>Doğrulu Kelime Sayısı: {{trueCount}}</span> <br>
          <span>Yanlış Kelime Sayısı: {{falseCount}}</span> <br>
          <button class="mt-2 btn btn-secondary btn-lg btn-block" @click="newGame"> Yeni Oyun</button>
        </div>
        <div v-else>
            <div class="card">
                <div class="card-body">
                    <span v-for="(word, key) in words.filter((data, index)=>index<20)" :key="key" v-bind:class="key!=0 || writingWordControl " class="words ml-2">
                        {{word}}
                    </span>
                </div>
            </div>
            <div class="card mt-1">
                <div class="card-body bg-secondary">
                    <div class="input-group input-group-lg">
                        <input type="text" class="form-control" v-model="writingWord">
                        <div class="input-group-append ml-1" id="button-addon4">
                            <span class="btn btn-outline-secondary btn-light" type="button">{{timer}} sn.</span>
                            <button :disabled="isRunning" class="btn btn-outline-secondary btn-light" type="button" @click="getWords()">Yenile</button>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>

import wordList from '@/assets/words.json'

export default {
  data () {
    return {
      words: [],
      writingWord: null,
      isTrue: true,
      trueCount: 0,
      falseCount: 0,
      timer: 5,
      interval: false,
      isRunning: false,
      isFinish: false,
      wordList: wordList
    }
  },

  watch: {
    writingWord (val) {
      if (!val || val === ' ') {
        this.writingWord = ''
        return
      }
      if (!this.isRunning) this.toggleTimer()
      const word = this.words[0].slice(0, val.length).toLowerCase()
      const userWord = val.replace(' ', '').toLowerCase()

      this.isTrue = word === userWord

      if (val.indexOf(' ') !== -1) {
        this.isTrue ? this.trueCount++ : this.falseCount++
        this.words.splice(0, 1)
        this.writingWord = ''
        this.isTrue = true
      }
    }
  },

  mounted () {
    this.getWords()
  },

  computed: {
    writingWordControl () {
      return this.isTrue ? 'writing-word' : 'writing-word bg-danger'
    },
    dks () {
      return 300 - this.words.length // =trueCount + falseCount
    },
    truePercent () {
      return isNaN((100 * (this.trueCount / this.dks)).toFixed(2)) ? 0 : (100 * (this.trueCount / this.dks)).toFixed(2)
    }
  },

  methods: {
    newGame () {
      this.getWords()
      this.isFinish = false
      this.timer = 60
      this.isTrue = true
      this.isRunning = false
    },
    toggleTimer () {
      this.isRunning = true
      this.interval = setInterval(() => {
        if (this.timer === 0) {
          clearInterval(this.interval)
          this.isFinish = true
          return
        }
        this.timer--
      }, 1000)
    },

    getWords () {
      this.words = this.wordList.sort(() => Math.random() - 0.5).splice(0, 300)
    }
  }
}
</script>

<style>
    .words {
        font-size: 22px;
        font-weight: 400;
    }

    .writing-word{
        background-color: slategrey;
        color: white;
        padding: 5px;
        border-radius: 5px;
    }
</style>
