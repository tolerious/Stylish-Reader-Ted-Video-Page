<template>
  <div class="close-button-container" @click="closeWindow">
    <img
      src="https://stylishreader.oss-cn-beijing.aliyuncs.com/cancel.png"
      alt=""
    />
  </div>
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
      <video id="player" playsinline controls style="width: 100%">
        <source :src="videoUrl" type="video/mp4" />
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
        <p>{{ enTranscript[0]?.text }}</p>
        <p>-----------------</p>
      </div>
    </div>
    <div
      style="
        border-top: 1px solid rgba(255, 255, 255, 0.2);
        background-color: #0f0f0f;
        grid-area: left-bottom;
        color: white;
        display: grid;
        grid-template-columns: 70% 30%;
        grid-template-rows: 1fr;
        align-items: center;
      "
    >
      <div style="padding: 0 5px; box-sizing: border-box; font-size: 14px">
        <span>Stay Hungry Stay Foolish... </span>
      </div>
      <div
        style="
          display: flex;
          flex-direction: row;
          justify-content: space-between;
          padding: 0 5px;
          font-size: 14px;
          box-sizing: border-box;
          color: rgba(255, 255, 255, 0.8);
        "
      >
        <span style="cursor: pointer">Contact</span>
        <span style="cursor: pointer; color: #ffc862">Reward Me</span>
        <span style="cursor: pointer">Feature Request</span>
      </div>
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
      <div
        style="color: rgba(255, 255, 255, 0.8)"
        v-for="trans in enTranscript"
      >
        <div style="margin-bottom: 20px; color: rgba(255, 255, 255, 0.8)">
          <span style="display: block">{{ trans.text }} </span>
          <!-- <span style="display: block">我们今天将要学习如何学习英语</span> -->
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import Plyr from "plyr";
import { computed, onMounted, ref } from "vue";

const videoUrl = ref("");

const player = ref(null);

const transcriptList = new Map();

const enTranscript = ref([]);

function initializeVideo() {
  player.value = new Plyr("#player", {
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
  player.value.on("loadeddata", (event) => {
    console.log("stylish custom video loaded...");
  });
}

function closeWindow() {
  player.value.pause();
  sendMessageToContentScript({ type: "close-popup", message: "" });
}

function eventListenerFromContent() {
  document.addEventListener("fromContentScript", (event) => {
    const detail = JSON.parse(event.detail);
    // console.log(detail);
    switch (detail.type) {
      case "update-video-source":
        console.log(detail.videoUrl, video.value);
        if (!video.value || video.value !== detail.videoUrl) {
          console.log("set video url...");
          video.value = detail.videoUrl;
          player.value.source = {
            type: "video",
            title: "Powered by Stylish Reader",
            poster:
              "https://stylishreader.oss-cn-beijing.aliyuncs.com/cover-with-enjoy-text.jpg",
            sources: [
              {
                src: video.value,
                type: "video/mp4",
                // size: 720,
              },
            ],
          };
        }

        break;
      case "webvtt":
        transcriptList.set(
          detail.data.code,
          JSON.parse(JSON.parse(detail.data.data))
        );
        if (detail.data.code === "en") {
          enTranscript.value = transcriptList.get("en");
        }
        break;
      case "languages":
        // do something
        console.log(detail.supportedLanguages);
        break;
      default:
        break;
    }
  });
}

function sendMessageToContentScript(message) {
  const event = new CustomEvent("fromInjectScript", {
    detail: JSON.stringify(message),
  });
  document.dispatchEvent(event);
}

onMounted(() => {
  initializeVideo();
  eventListenerFromContent();
  console.log(`Vue show time:${new Date().getTime()}`);
});
</script>

<style scoped>
.close-button-container {
  position: fixed;
  top: 15px;
  left: 15px;
  z-index: 99999999;
  height: 20px;
  width: 20px;
  cursor: pointer;
  img {
    height: 100%;
  }
}
</style>
