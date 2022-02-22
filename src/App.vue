<template>
  <div id="app">
    <div id="nav">
      <router-link to="/">Home</router-link> |
      <router-link to="/about">About</router-link>
    </div>
    <router-view />
    <div class="box_player">
      <div class="left_control">
        <div class="top" @click="nextMusic"></div>
        <div v-if="isPlay" @click="changePlay(false)" class="mid"></div>
        <div v-if="!isPlay" @click="changePlay(true)" class="mid2"></div>
        <div class="bot" @click="nextMusic"></div>
      </div>
      <Slider style="flex: 1" :show-tooltip="false" v-model="value" />
      <div  class="right">
        <div style="width:100%;height:100%" @click="isShow = !isShow"></div>
        <Slider v-if="isShow" @change="changeVoice" class="right_slider" style="height:100px" :show-tooltip="false" vertical v-model="value1" />
      </div>
    </div>
    <audio ref="audio" style="display: none" :src="src"></audio>
  </div>
</template>
<script>
import { Slider } from "element-ui";
export default {
  components: {
    Slider,
  },
  data() {
    return {
      src: "",
      isPlay: false,
      audio: null,
      isShow:false,
      value1:'10',
      value: 0,
      srcList: [require("./assets/m1.mp3"), require("./assets/m2.mp3")],
      currentIndex: 0,
      timer: null,
    };
  },
  mounted() {
    this.$nextTick(() => {
      this.audio = this.$refs.audio;
      this.audio.src = this.srcList[1];
      this.setTimer();
      document.querySelector(".el-slider__button-wrapper").onmousedown = () => {
        if (this.timer) {
          clearInterval(this.timer);
        }
      };
      document.querySelector(".el-slider__button-wrapper").onmouseup = () => {
        this.audio.currentTime = (this.value * this.audio.duration) / 100;
        this.audio.play();
        if (!this.isPlay) {
          this.isPlay = true;
        }
        this.setTimer();
      };
    });
  },
  methods: {
    changeVoice(e){
      this.audio.volume = e / 100
    },
    setTimer() {
      if (this.timer) {
        clearInterval(this.timer);
      }
      this.timer = setInterval(() => {
        this.value = this.audio
          ? Number(
              ((this.audio.currentTime / this.audio.duration) * 100).toFixed(0)
            )
          : 0;
      }, 10);
    },
    changePlay(flag) {
      this.isPlay = flag;
      if (this.isPlay) {
        this.audio.play();
      } else {
        this.audio.pause();
      }
    },
    nextMusic() {
      //此处应该是后端请求数据
      let src = this.srcList[this.currentIndex];
      this.currentIndex = this.currentIndex == 0 ? 1 : 0;
      this.onPlay(src);
      this.changePlay(true);
    },
    onPlay(src) {
      if (!this.audio) {
        return;
      }
      this.audio.src = src;
      this.audio.autoplay = true;
    },
  },
  beforeDestroy() {
    clearInterval(this.timer);
  },
};
</script>
<style>
* {
  margin: 0;
  padding: 0;
}
.box_player {
  width: 700px;
  height: 60px;
  border: 1px solid #ccc;
  border-radius: 10px;
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  margin: auto;
  display: flex;
  justify-content: space-between;
  align-items: center;
  background: #000000;
}
.left_control {
  display: flex;
  align-items: center;
  margin: 0 20px;
}
.top {
  width: 26px;
  height: 26px;
  background: url("./assets/1645495560.jpg");
  background-position: -1px 485px;
  border-radius: 50%;
  cursor: pointer;
}
.top:hover {
  background-position: -31px 485px;
}
.bot {
  width: 26px;
  height: 26px;
  background: url("./assets/1645495560.jpg");
  background-position: -80px 485px;
  border-radius: 50%;
  cursor: pointer;
}
.bot:hover {
  background-position: -110px 485px;
}
.mid {
  width: 33px;
  height: 33px;
  background: url("./assets/1645495560.jpg");
  background-position: -1px 450px;
  border-radius: 50%;
  cursor: pointer;
  margin: 0 5px;
}
.mid:hover {
  background-position: -41px 450px;
}
.mid2 {
  width: 33px;
  height: 33px;
  background: url("./assets/1645495560.jpg");
  background-position: -1px 411px;
  border-radius: 50%;
  cursor: pointer;
  margin: 0 5px;
}
.mid2:hover {
  background-position: -41px 411px;
}
.right {
  width: 12px;
  height: 20px;
  background: url("./assets/1645495560.jpg");
  background-position: -110px -71px;
  border-radius: 50%;
  cursor: pointer;
  margin: 0 20px;
  position: relative;
  
}
.right_slider{
  position: absolute;
  bottom: 130px;
  right: 12px;;
}
.right:hover {
  background-position: -132px -71px;
}
</style>
