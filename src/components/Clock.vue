<script setup lang="ts">
import { reactive, ref, toRef, watch } from "vue";

const props = defineProps<{
  digit: number;
}>();
function sleep(ms: number) {
  return new Promise((r) => {
    setTimeout(() => r(true), ms);
  });
}

const digit = toRef(props, "digit");
const next = ref(digit.value);
const prev = ref(digit.value);
const anim = reactive({ play: false });
watch(digit, async (newVal, oldVal) => {
  next.value = newVal;
  prev.value = oldVal;
  anim.play = true;
  await sleep(800);
  anim.play = false;
  prev.value = newVal;
  next.value = newVal;
});
</script>

<template>
  <div class="clock">
    <div class="clock__inner" :class="anim">
      <div class="display">
        <div class="display__top">{{ next }}</div>
        <div class="display__bottom">{{ prev }}</div>
      </div>
      <div class="flipper">
        <div class="flipper__top">{{ prev }}</div>
        <div class="flipper__bottom">{{ next }}</div>
      </div>
    </div>
  </div>
</template>

<style lang="scss" scoped>
/****************************************************************************\
 * FrontEndFunn: https://github.com/frontendfunn/flipclock-using-javascript *
\****************************************************************************/
$flip-height: 100px;
$flip-width: calc($flip-height * 0.6);
$line-height: calc($flip-height * 0.5);
$flip-container-color: #131313;
$flip-color: #131313;
$flip-text-color: #f2aa3c;
$flip-border-radius: calc($flip-height * 0.05);
$animation-time: 1s;
$animation-ease: linear;
$perspective: 200px;
@keyframes flipperTopAnimation {
  0% {
    transform: rotateX(0deg);
  }
  50%,
  100% {
    transform: rotateX(-90deg);
    opacity: 1;
  }
  100% {
    opacity: 0;
  }
}
@keyframes flipperBottomAnimation {
  0%,
  50% {
    transform: rotateX(90deg);
  }
  100% {
    transform: rotateX(0deg);
  }
}
@mixin flipper {
  position: absolute;
  left: -10%;
  right: -10%;
  width: $flip-width;
  margin: auto;
  text-align: center;
  height: calc($flip-height * 0.5);
  line-height: calc($line-height * var(--i));
  background-color: $flip-color;
  overflow: hidden;
  color: $flip-text-color;
}
@mixin display {
  position: relative;
  text-align: center;
  overflow: hidden;
  width: 100%;
  height: calc($flip-height * 0.5);
  color: $flip-text-color;
  background-color: $flip-container-color;
  line-height: calc($line-height * var(--i));
}
.clock {
  border-radius: 0.5rem;
  position: relative;
  display: flex;
  flex-shrink: 0;
  &__inner {
    height: $flip-height;
    width: $flip-width;
    border-radius: 4px;
    font-size: calc($flip-height * 0.5);
    font-weight: 700;
    position: relative;
    margin: 0.25rem;
  }
  .play {
    .flipper__top {
      animation: flipperTopAnimation $animation-time $animation-ease;
    }
    .flipper__bottom {
      animation: flipperBottomAnimation $animation-time $animation-ease;
    }
  }
  .display {
    height: $flip-height;
    width: $flip-width;
    display: flex;
    flex-direction: column;
    z-index: 1;
    &__top {
      @include display;
      --i: 2;
      border-top-left-radius: $flip-border-radius;
      border-top-right-radius: $flip-border-radius;
    }
    &__bottom {
      @include display;
      --i: -2;
      border-bottom-left-radius: $flip-border-radius;
      border-bottom-right-radius: $flip-border-radius;
    }
  }
  .flipper {
    position: absolute;
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
    margin: auto;
    height: $flip-height;
    width: $flip-width;
    perspective: $perspective;
    &__top {
      @include flipper;
      --i: 2;
      top: 0;
      transform-origin: bottom;
      border-top-left-radius: $flip-border-radius;
      border-top-right-radius: $flip-border-radius;
    }
    &__bottom {
      @include flipper;
      --i: -2;
      bottom: 0;
      transform: rotateX(90deg);
      transform-origin: top;
      border-bottom-left-radius: $flip-border-radius;
      border-bottom-right-radius: $flip-border-radius;
    }
  }
}
</style>
