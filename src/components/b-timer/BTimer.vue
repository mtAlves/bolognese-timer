<template>
  <div class="container">
    <div class="controls">
      <BTimerButton v-if="stopped" @click.native="toggleTimer()" label="Resume" button-color="#54acef" icon="play-circle" />
      <BTimerButton v-else @click.native="toggleTimer()" label="Pause" button-color="#54acef" icon="pause-circle" />
      <BTimerButton @click.native="resetTimer()" label="Reset" button-color="#ffd34e" icon="stop-circle" />
      <BTimerButton label="Settings" icon="settings" />
    </div>
    <div class="timer-value">
      <span>{{time}}</span>
    </div>
  </div>
</template>

<script>
import { BTimerButton } from '@/components/b-timer-button';
import moment from 'moment';

export default {
  name: 'BTimer',
  props: {
    initialTime: {
      required: true,
      type: String
    }
  },
  components: {
    BTimerButton
  },
  data() {
    return {
      time: this.initialTime,
      primitiveTime: moment(this.initialTime, 'mm:ss'),
      timerInterval: null,
      stopped: false
    };
  },
  computed: {
    actualTime() {
      return this.time;
    }
  },
  mounted() {
    this.init();
  },
  methods: {
    init() {
      this.timerInterval = setInterval(() => {
        this.decrementTime();
      }, 1000);
    },
    resetTimer() {
      this.time = this.initialTime;
      this.primitiveTime = moment(this.initialTime, 'mm:ss');
    },
    stopTimer() {
      window.clearInterval(this.timerInterval);
    },
    toggleTimer() {
      if (this.stopped) this.init();
      else this.stopTimer();

      this.stopped = !this.stopped;
    },
    decrementTime() {
      if (this.finishedTimer()) {
        this.notify();
        return;
      }
      this.primitiveTime.add(-1, 'seconds');
      this.time = this.primitiveTime.format('mm:ss');
    },
    finishedTimer() {
      return this.time === '00:00';
    },
    notify() {
      this.stopTimer();
      // TODO:Notify parent component
    }
  },
  watch: {
    actualTime() {
      document.title = `${this.time} ${this.$route.name}`;
    }
  }
};
</script>

<style lang="scss" scoped>
@import '~@scss-variables';

.container {
  display: flex;
  >.timer-value {
    display: flex;
    color: $primary;
    justify-content: center;
  }
  >.controls {
    display: flex;
    justify-content: space-around;
  }
}

@media screen and(min-width:$mobile-screen-size) {
  .container {
    box-shadow: 0 1px 2px rgba(0, 0, 0, .23);
    width: $mobile-screen-size;
    margin: 0 auto;
    height: 400px;
    font-size: 100px;
    align-items: center;
    flex-flow: column wrap;
    box-sizing: border-box;
    justify-content: center;
    padding: 1rem 0;
    box-sizing: border-box;
    >.timer-value {
      order: 1;
      align-items: center;
      flex-grow: 1;
      width: 100%;
    }
    >.controls {
      order: 2;
      width: 100%;
    }
  }
}

@media screen and(max-width:$mobile-screen-size) {
  .container {
    width: 100%;
    box-sizing: border-box;
    flex-flow: column wrap;
    >.timer-value {
      order: 2;
      flex-grow: 1;
      font-size: 80px;
      display: flex;
      align-items: center;
    }
    >.controls {
      order: 1;
      padding: 1rem 0;
    }
  }
}
</style>
