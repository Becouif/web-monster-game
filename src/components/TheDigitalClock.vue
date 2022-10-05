<template>
  <div>
    <div class="LCD">
      <div class="hours">{{ hours }}</div>
      <div class="divider">:</div>
      <div class="minutes">{{ minutes }}</div>
      <div class="divider">:</div>
      <div class="seconds">{{ seconds }}</div>
      <span class="section"> {{ ' ' + section }} </span>
    </div>
  </div>
</template>
<script>
export default {
  name: 'DigitalClock',
  data() {
    return {
      hours: 0,
      minutes: 0,
      seconds: 0,
      section: 'AM',
    };
  },
  mounted() {
    setInterval(() => this.setTime(), 1000);
  },
  methods: {
    setTime() {
      const date = new Date();
      let hours = date.getHours();
      let minutes = date.getMinutes();
      let seconds = date.getSeconds();

      hours = hours <= 9 ? `${hours}`.padStart(2, 0) : hours;
      minutes = minutes <= 9 ? `${minutes}`.padStart(2, 0) : minutes;
      seconds = seconds <= 9 ? `${seconds}`.padStart(2, 0) : seconds;
      if (hours > 12) {
        hours = hours - 12;
        this.section = 'PM';
      }
      this.hours = hours;
      this.minutes = minutes;
      this.seconds = seconds;
    },
  },
};
</script>
<style scoped>
.LCD {
  display: flex;
  font-style: no;
}
.LCD > div {
  font-family: 'alarm clock';
  font-size: x-large;
}
.section {
  font-size: large;
  margin-top: 0.35rem;
}
</style>
