<template>
  <div class="container">
    <template v-if="status === 'HOME'">
      <div class="title">Today workout</div>
      <div class="subtitle">
        You're making progress, Keep going!
      </div>

      <button class="button mg-t-3" @click="start">
        Start workout
      </button>
    </template>
    <template v-if="status === 'STARTED'">
      <div class="step" v-if="currentStepData.type === 'REST'">
        <div class="title">Take a rest</div>
        <div class="timer">
          {{ timerDisplay }}
        </div>

        <div class="rest">
          <div class="rest-summary">
            {{currentExcersie}}/{{totalExercises}}
          </div>

          <div class="rest-next" @click="nextStep">
            <div class="next-title">
              Next
            </div>

            <div class="next-content">
              Jumping Jack: 30 seconds
            </div>
          </div>
        </div>
      </div>

      <div class="step" v-if="currentStepData.type === 'HOLD'">
        <div class="step-img">
          <img :src="currentStepData.image" />
        </div>
        <div class="title">{{currentStepData.name}}</div>
        <div class="timer">
          {{ timerDisplay }}
        </div>
        <button class="button" @click="nextStep">
          Skip
        </button>
      </div>

      <div class="step" v-if="currentStepData.type === 'REP'">
        <div class="step-img">
          <img :src="currentStepData.image" />
        </div>
        <div class="title">{{currentStepData.name}}</div>
        <div class="quantity">
          x {{currentStepData.quantity}}
        </div>
        <button class="button" @click="nextStep">
          DONE
        </button>
      </div>

    </template>
    <template v-if="status === 'DONE'">
      <div class="done">
        <svg width="96" height="96" viewBox="0 0 96 96" fill="none" xmlns="http://www.w3.org/2000/svg">
          <path d="M48 68C52.2435 68 56.3131 66.3143 59.3137 63.3137C62.3143 60.3131 64 56.2435 64 52H32C32 56.2435 33.6857 60.3131 36.6863 63.3137C39.6869 66.3143 43.7565 68 48 68Z" fill="black"/>
          <path d="M40 40C40 41.0609 39.5786 42.0783 38.8284 42.8284C38.0783 43.5786 37.0609 44 36 44C34.9391 44 33.9217 43.5786 33.1716 42.8284C32.4214 42.0783 32 41.0609 32 40C32 38.9391 32.4214 37.9217 33.1716 37.1716C33.9217 36.4214 34.9391 36 36 36C37.0609 36 38.0783 36.4214 38.8284 37.1716C39.5786 37.9217 40 38.9391 40 40Z" fill="black"/>
          <path d="M60 44C61.0609 44 62.0783 43.5786 62.8284 42.8284C63.5786 42.0783 64 41.0609 64 40C64 38.9391 63.5786 37.9217 62.8284 37.1716C62.0783 36.4214 61.0609 36 60 36C58.9391 36 57.9217 36.4214 57.1716 37.1716C56.4214 37.9217 56 38.9391 56 40C56 41.0609 56.4214 42.0783 57.1716 42.8284C57.9217 43.5786 58.9391 44 60 44Z" fill="black"/>
          <path fill-rule="evenodd" clip-rule="evenodd" d="M88 48C88 70.092 70.092 88 48 88C25.908 88 8 70.092 8 48C8 25.908 25.908 8 48 8C70.092 8 88 25.908 88 48ZM80 48C80 56.4869 76.6286 64.6263 70.6274 70.6274C64.6263 76.6286 56.4869 80 48 80C39.5131 80 31.3737 76.6286 25.3726 70.6274C19.3714 64.6263 16 56.4869 16 48C16 39.5131 19.3714 31.3737 25.3726 25.3726C31.3737 19.3714 39.5131 16 48 16C56.4869 16 64.6263 19.3714 70.6274 25.3726C76.6286 31.3737 80 39.5131 80 48Z" fill="black"/>
        </svg>
        <div class="title">Well done</div>
        <div class="subtitle">
          You’ve completed the workout today
        </div>
      </div>
    </template>
  </div>
</template>

<script>
export default {
  name: 'Home',
  data() {
    return {
      status: 'HOME',
      currentStep: 0,
      currentExcersie: 0,
      totalExercises: 0,
      timer: 0,
      timerInterval: null,
      currentStepData: {},
      steps: []
    }
  },
  mounted() {
    const data = require('./data.json')
    this.steps = data.steps
  },
  computed: {
    timerDisplay() {
      const minutes = Math.floor(this.timer/60)
      const seconds = this.timer % 60
      return  `${minutes} : ${seconds}`
    },
  },
  methods: {
    start() {
      this.status = "STARTED"
      this.currentStep = 0
      this.currentStepData = this.steps[0]
      this.totalExercises = this.steps.filter((s) => s.type != 'REST').length
      this.currentExcersie = 0
      this.runStep()
    }, 
    nextStep() {
      if(this.timerInterval) {
        clearInterval(this.timerInterval)
      }

      this.currentStep++
      if (this.currentStep >= this.steps.length) {
        this.done()
        return
      }

      this.currentStepData = this.steps[this.currentStep]
      this.runStep()
    },
    runStep() {
      if(this.currentStepData.type != 'REST') {
        this.currentExcersie++
      }

      if(this.currentStepData.type === 'HOLD' || this.currentStepData.type === 'REST') {
        this.timer = this.currentStepData.quantity
        this.timerInterval = setInterval(() => {
          this.timer--

          if (this.timer <= 0) {
            this.nextStep()
          }
        }, 1000)
      }
    },
    done() {
      this.status = "DONE"
    },
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Roboto:wght@400;700;900&display=swap');

.container {
  font-family: Roboto;
  font-size: 14px;
}

.title {
  font-style: normal;
  font-weight: 700;
  font-size: 2.5rem;
  text-transform: uppercase;
  line-height: 47px;
  /* identical to box height */
  letter-spacing: 0.18em;
  color: #000;
  padding: 24px;
}

.subtitle {
  font-size: 1.4rem;
  color: #828282;
}

.step-img img {
  max-height: 25vh;
}

.button {
  background: #fff;
  padding: 16px 24px;
  border: 2px solid #333333;
  box-sizing: border-box;
  box-shadow: 0px 4px 0px #000000;
  border-radius: 8px;
  font-size: 24px;
  margin-top: 40px;
  cursor: pointer;
}

.button:hover {
  background: #dfdfdf;
}

.button:active {
  box-shadow: 0 0 0;
  transform: translateY(4px);
}

.timer, .quantity {
  font-size: 40px;
  font-weight: 900;
  color: #000;
}

.rest {
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
  border: 1px solid #333333;
  border-radius: 8px;
  padding: 16px;
  width: fit-content;
  margin: auto;
  font-size: 24px;
  margin-top: 32px;
}

.rest-summary {
  padding: 16px;
  font-weight: 700;
  font-size: 32px;
}

.rest-next {
  text-align: left;
}

.next-title {
  color: #828282;
  padding: 4px 0;
}
</style>
