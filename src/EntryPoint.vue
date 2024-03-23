<template>
  <div
    style="
      height: 100%;
      width: 100%;
      position: fixed;
      top: 0;
      background-color: #eeeeee;
      display: grid;
      grid-template-columns: 1fr 25%;
      grid-template-rows: auto 280px 50px;
      grid-template-areas: 'left-top right-bar' 'left-middle right-bar' 'left-bottom right-bar';
    "
  >
    <div
      style="
        grid-area: left-top;
        background-color: #0f0f0f;
        color: white;
        overflow: hidden;
        padding: 20px;
        box-sizing: border-box;
        display: flex;
        flex: 1;
      "
    >
      <video id="player" playsinline controls style="">
        <source
          src="https://download.ted.com/products/177106.mp4?apikey=acme-roadrunner"
          type="video/mp4"
        />
      </video>
    </div>
    <div
      style="
        grid-area: left-middle;
        background-color: #0f0f0f;
        color: rgba(255, 255, 255, 0.8);
        font-size: 23px;
        display: grid;
        place-items: center;
        font-weight: 500;
      "
    >
      <div style="text-align: center">
        <p>Today, we have a topic about how to learn English</p>
        <p>今天我们有一个如何学习英语的话题</p>
      </div>
    </div>
    <div
      style="
        border-top: 1px solid rgba(255, 255, 255, 0.2);
        background-color: #0f0f0f;
        grid-area: left-bottom;
        color: white;
      "
    >
      left-bottom
    </div>
    <div
      id="stylish-reader-popup-right-container"
      style="
        border-left: 1px solid rgba(255, 255, 255, 0.2);
        color: rgba(255, 255, 255, 0.8);
        background-color: #0f0f0f;
        grid-area: right-bar;
        padding: 20px;
        box-sizing: border-box;
        overflow-y: auto;
      "
    >
      <div style="color: rgba(255, 255, 255, 0.8)">
        <div style="margin-bottom: 20px; color: #ffc862">
          <span style="display: inline-block; margin-bottom: 10px"
            >Today, we have a topic about how to learn English, we will
            recommend some tools for you.</span
          >
          <span style="display: inline-block"
            >我们今天将要学习如何学习英语</span
          >
        </div>
      </div>
      <div style="color: rgba(255, 255, 255, 0.8)">
        <div style="margin-bottom: 20px">
          <span style="display: inline-block; margin-bottom: 10px"
            >Today, we have a topic about how to learn English, we will
            recommend some tools for you.</span
          >
          <span style="display: inline-block"
            >我们今天将要学习如何学习英语</span
          >
        </div>
      </div>
      <div>
        <p>
          Today, we have a topic about how to learn English, we will recommend
          some tools for you.
        </p>
        <p>我们今天将要学习如何学习英语</p>
      </div>
    </div>
  </div>
</template>

<script setup>
import { computed, onMounted } from "vue";
import plyrSvg from "./assets/plyr.svg";

function initializeVideo() {
  const player = new Plyr("#player", {
    title: "123",
    // debug: true,
    controls: [
      "play-large",
      "play",
      "progress",
      "current-time",
      "mute",
      "volume",
      "captions",
      "settings",
      "pip",
      "airplay",
      "fullscreen",
      // additional controls
      "rewind",
      "fast-forward",
      "duration",
      "restart",
    ],
    // loadSprite: false,
    // iconUrl: 'https://stylishreader.oss-cn-beijing.aliyuncs.com/plyr.svg',
  });
}

const svgBase64 = computed(() => btoa(plyrSvg));

const svgDataUrl = computed(
  () => `data:image/svg+xml;base64,${svgBase64.value}`
);
onMounted(() => {
  console.log(plyrSvg);
  const svgBase64 = btoa(plyrSvg);
  initializeVideo();
});
</script>

<style scoped></style>
