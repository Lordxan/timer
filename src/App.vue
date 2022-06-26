<script setup lang="ts">
import extend from "moment-duration-format";
import moment, { Moment } from "moment";
import { ref } from "vue";
import Clock from "./components/Clock.vue";

extend(moment as any);

const endgame = moment("2023-06-09");

const getDuration = (e: Moment) => () =>
  moment
    .duration(e.diff(new Date()))
    .format("MM:DD:hh:mm:ss", { trim: false })
    .split(":");

const times = ref<string[]>([]);
const tick = getDuration(endgame);

const interval = setInterval(() => {
  let value = tick();
  if (value.flat().some((e) => e.includes("-"))) {
    value = value.map((e) => e.replace("-", ""));
    clearInterval(interval);
  }
  times.value = value;
}, 1000);

const src = new URL("https://www.youtube-nocookie.com/embed/qHKMF2zGrAg");
src.searchParams.append("controls", "0");
// src.searchParams.append("start", "8");
src.searchParams.append("autoplay", "1");
src.searchParams.append("rel", "1");
src.searchParams.append("showinfo", "0");
</script>

<template>
  <iframe
    class="video"
    :src="src.toString()"
    title="YouTube video player"
    frameborder="0"
    allow="autoplay;"
  />
  <template v-for="(time, i) in times" :key="i">
    <div class="time">
      <clock
        v-for="(value, j) in time.split('')"
        :key="j"
        :digit="Number(value)"
      />
    </div>
  </template>
</template>

<style lang="scss">
* {
  box-sizing: border-box;
}
body,
html {
  height: 100%;
  margin: 0;
}
#app {
  height: 100%;
  font-family: "Anton", sans-serif;
  display: flex;
  justify-content: center;
  align-items: center;
}
.time {
  display: flex;
  justify-content: space-evenly;
  align-items: center;
  margin: 0 10px;
}
.video {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
}
</style>
