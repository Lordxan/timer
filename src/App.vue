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

const video = ref<URL>(
  new URL("https://www.youtube-nocookie.com/embed/rNM3yGQIdrE")
);
video.value.searchParams.append("controls", "0");
video.value.searchParams.append("autoplay", "1");
video.value.searchParams.append("mute", "1");
video.value.searchParams.append("rel", "1");
video.value.searchParams.append("showinfo", "0");

const setIcon = (muted = false) =>
  `url(${muted ? "'/volume_off.svg'" : "'/volume_on.svg'"})`;
const icon = ref(setIcon());
function toggleSound() {
  const muted = video.value.searchParams.has("mute");
  icon.value = setIcon(muted);
  if (muted) {
    video.value.searchParams.delete("mute");
  } else {
    video.value.searchParams.append("mute", "1");
  }
}
</script>

<template>
  <iframe
    class="video"
    :src="video.toString()"
    title="YouTube video player"
    frameborder="0"
    allow="autoplay;"
  />
  <i class="toggle" @click="toggleSound" />
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
$icon-size: 64px;
.toggle {
  position: absolute;
  right: 100px;
  top: 10px;
  width: $icon-size;
  height: $icon-size;
  background-repeat: no-repeat;
  background-position: center;
  background-size: contain;
  cursor: pointer;
  background-image: v-bind(icon);
  filter: invert(80%) sepia(22%) saturate(1374%) hue-rotate(337deg)
    brightness(99%) contrast(82%);
}
</style>
