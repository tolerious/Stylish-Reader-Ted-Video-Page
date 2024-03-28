<template>
  <div class="close-button-container" @click="closeWindow">
    <img
      src="https://stylishreader.oss-cn-beijing.aliyuncs.com/cancel.png"
      alt=""
    />
  </div>
  <div
    class="stylish-reader-popup-container"
    style="
      height: 100%;
      width: 100%;
      position: fixed;
      top: 0;
      background-color: #eeeeee;
      display: grid;
      grid-template-columns: 1fr 25% 25%;
      grid-template-rows: auto 280px 50px;
      grid-template-areas: 'left-top right-bar right-right-bar' 'left-middle right-bar right-right-bar' 'left-bottom right-bar right-right-bar';
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
        flex-direction: row;
        justify-content: center;
        flex: 1;
        text-align: center;
      "
      class="video-container"
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
        <p>{{ currentEnTranslation.text }}</p>
        <p>{{ currentZhTranslation.text }}</p>
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
        <span style="cursor: pointer">联系我</span>
        <span style="cursor: pointer; color: #ffc862">赞助我</span>
        <span style="cursor: pointer">需求提交</span>
      </div>
    </div>
    <div
      id="stylish-reader-popup-right-container"
      class="stylish-reader-popup-container"
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
        <div
          style="margin-bottom: 20px"
          class="not-highlight"
          :class="{ highlight: timeInRange(trans.start, trans.end) }"
        >
          <span style="display: block">{{ trans.text }} </span>
        </div>
      </div>
    </div>
    <div
      class="stylish-reader-popup-container"
      style="
        border-left: 1px solid rgba(255, 255, 255, 0.2);
        color: rgba(255, 255, 255, 0.8);
        background-color: #0f0f0f;
        grid-area: right-right-bar;
        padding: 20px;
        box-sizing: border-box;
        overflow-y: auto;
      "
    >
      <div
        style="color: rgba(255, 255, 255, 0.8)"
        v-for="trans in zhTranscript"
      >
        <div
          style="margin-bottom: 20px"
          class="not-highlight"
          :class="{ 'highlight-right': timeInRange(trans.start, trans.end) }"
        >
          <span style="display: block">
            {{ trans.text }}
          </span>
        </div>
      </div>
      <div v-if="!hasZhTranslation">
        <span style="display: block">
          该视频尚未翻译，机器翻译正在开发中。<span
            style="font-style: italic"
          ></span
          >...
        </span>
        <div>
          <span
            style="
              text-decoration: underline;
              font-style: italic;
              cursor: pointer;
            "
            >赞助我，加速开发！</span
          >
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import Plyr from "plyr";
import { onMounted, ref } from "vue";

const videoUrl = ref("");

const player = ref(null);

const enTranscript = ref([]);

const zhTranscript = ref([]);

const currentTime = ref(0);

const currentEnTranslation = ref({});

const currentZhTranslation = ref({});

const hasZhTranslation = ref(false);

function timeStringToSeconds(timeString) {
  const parts = timeString.split(":").map((part) => parseFloat(part));
  return parts[0] * 3600 + parts[1] * 60 + parts[2];
}

function timeInRange(start, end) {
  const startSeconds = timeStringToSeconds(start);
  const endSeconds = timeStringToSeconds(end);
  return currentTime.value >= startSeconds && currentTime.value <= endSeconds;
}

function getCurrentTranslation() {
  enTranscript.value.forEach((item) => {
    if (timeInRange(item.start, item.end)) {
      currentEnTranslation.value = item;
    }
  });
  zhTranscript.value.forEach((item) => {
    if (timeInRange(item.start, item.end)) {
      currentZhTranslation.value = item;
    }
  });
}

function scrollElement() {
  const element = document.querySelector(".highlight");
  if (element) {
    element.scrollIntoView({
      behavior: "smooth",
      block: "center",
      inline: "nearest",
    });
  }
  const elementCopy = document.querySelector(".highlight-right");
  if (elementCopy) {
    elementCopy.scrollIntoView({
      behavior: "smooth",
      block: "center",
      inline: "nearest",
    });
  }
}

function initializeVideo() {
  player.value = new Plyr("#player", {
    title: "123",
    // debug: true,
    autoplay: true,
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
  player.value.on("canplay", (event) => {
    console.log("canplay");
  });
  player.value.on("timeupdate", (event) => {
    // console.log(player.value.currentTime);
    currentTime.value = player.value.currentTime;
    getCurrentTranslation();
    scrollElement();
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
        zhTranscript.value = JSON.parse(
          JSON.parse(JSON.parse(detail.data).zh.data)
        );
        enTranscript.value = JSON.parse(
          JSON.parse(JSON.parse(detail.data).en.data)
        );
        console.log(enTranscript.value);
        console.log(zhTranscript.value);
        console.log(enTranscript.value.length);
        console.log(zhTranscript.value.length);
        if (zhTranscript.value.length > 0) {
          hasZhTranslation.value = true;
        }
        break;
      case "languages":
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
  console.log(`Vue show time:${new Date().getTime().toLocaleString()}`);
  setInterval(() => {
    // console.log(zhTranscript.value);
    // console.log(enTranscript.value);
    // console.log(loopTranscript.value);
    // console.log(zhDic.value);
    // console.log(enDic.value);
    //   console.log(`loopTranscript: ${loopTranscript.value}`);
    console.log(hasZhTranslation.value);
  }, 1500);
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
.video-container {
  position: relative;
  img {
    bottom: 0;
    margin: auto;
  }
}

.not-highlight {
  color: rgba(255, 255, 255, 0.8);
}

.highlight,
.highlight-right {
  color: #ffc862;
}

.stylish-reader-popup-container {
  scrollbar-color: rgba(255, 255, 255, 0.8) #0f0f0f;
  scrollbar-width: thin;
}
</style>
