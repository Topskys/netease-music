<template>
  <div id="btmbar">
    <div>
      <div class="scroll">
        <el-slider
          v-model="musicDuration"
          :max="totalDuration"
          @change="musicDurationChange"
          :show-tooltip="false"
          :disabled="isUrl"
        ></el-slider>
      </div>
      <div class="content">
        <div class="left">
          <div class="singimg">
            <a href="javascript:void(0)" @click.stop="togSingel">
              <div class="s-img">
                <img :src="getNowMusicMenu.al.picUrl " alt />
                <div class="m-img">
                  <a href="javascript:void(0)" @click.stop="togSingel">
                    <i class="el-icon-arrow-up" style="color:rgba(255,255,255,0.8)"></i>
                    <i
                      class="el-icon-arrow-up"
                      style="color:rgba(255,255,255,0.8);position: absolute;top:12px;left:13px"
                    ></i>
                  </a>
                </div>
              </div>
            </a>
          </div>
          <div class="sing">
            <div>
              <a
                href
              >{{getNowMusicMenu.ar[0].name}} - {{getNowMusicMenu.name || getNowMusicMenu.al.name}}</a>
            </div>
            <div class="sing-time">
              <span>{{musicDuration | showTime}}</span>/
              <span>{{getTime}}</span>
            </div>
          </div>
          <div class="icon">
            <span style="cursor: pointer;">
              <i class="el-icon-star-off" v-show="true"></i>
              <i class="el-icon-star-on" v-show="false"></i>
            </span>
          </div>
          <div class="icon">
            <span style="cursor: pointer;">
              <i class="el-icon-download"></i>
            </span>
          </div>
          <div class="icon">
            <span style="cursor: pointer;">
              <i class="el-icon-chat-dot-square"></i>
            </span>
          </div>
          <div class="icon">
            <span style="cursor: pointer;">
              <i class="el-icon-more-outline"></i>
            </span>
          </div>
        </div>
        <div class="play">
          <div class="pre" @click="preMusic">
            <span style="cursor: pointer;">
              <i class="el-icon-caret-left"></i>
            </span>
          </div>
          <div class="start" @click="playMusic">
            <span style="cursor: pointer;">
              <i class="el-icon-video-pause" v-show="isplay"></i>
              <i class="el-icon-video-play" v-show="!isplay"></i>
            </span>
          </div>
          <div class="next" @click="nextMusic">
            <span style="cursor: pointer;">
              <i class="el-icon-caret-right"></i>
            </span>
          </div>
        </div>
        <div class="more">
          <div style="marginRight:30px" class="loundBox">
            <a href="#">
              <i class="el-icon-mic"></i>
            </a>
            <div class="lound">
              <el-slider v-model="value" vertical height="60px" size="mini" @change="volumeChange"></el-slider>
            </div>
          </div>
          <el-button type="primary" class="more-btn">
            ??????
            <i class="el-icon-arrow-up el-icon--right"></i>
          </el-button>
          <el-button type="primary" class="more-btn">
            ??????
            <i class="el-icon-arrow-up el-icon--right"></i>
          </el-button>
          <div>
            <a href="#">
              <i class="el-icon-s-data"></i>
            </a>
          </div>
          <div>
            <a href="#">
              <i class="el-icon-sort"></i>
            </a>
          </div>
          <div>
            <a href="#">
              <i class="el-icon-close-notification"></i>
            </a>
          </div>
          <div>
            <span @click="showRightList" style="cursor: pointer;">
              <i class="el-icon-s-unfold"></i>
            </span>
          </div>
        </div>
        <audio :src="getNowMusic" autoplay class="playMusicAudio" ref="audio"></audio>
      </div>
    </div>
    <el-dialog
            :modal="false"
            :show-close="false"
            title="????????????"
            :visible.sync="showRightDialog"
            width="23%"
            center
          >
          <LiterTable size='mini' :indexShow='false' :doShow='false' :anchorShow='false' :wth1='165' :wth2='130' :wth3='80'> </LiterTable>
    </el-dialog>
  </div>
</template>

<script>
import { mapGetters } from "vuex";
import  LiterTable from '@/components/middle/list/LiterTable'
import { timeFormat, songTimeFormat } from "@/util/uctil";
import {
  NOW_MUSICMENU,
  NOW_MUSIC,
  ADD_COUNT,
  SUB_COUNT,
  CLEAR_COUNT,
  SET_DURATION,
  SET_PLAY,
} from "@/store/mutationType";
import { getMusicUrl, getMusicMenu } from "@/network/home";
export default {
  name: "BottomBar",
  components:{
    LiterTable,
  },
  computed: {
    ...mapGetters([
      "getNowMusic",
      "getNowMusicMenu",
      "getMusicListIds",
      "getMusicCount",
      "getDuration",
    ]),
    getTime() {
      return timeFormat(this.getNowMusicMenu.dt);
    },
  },
  data() {
    return {
      isplay: false, //????????????
      musicDuration: 0, //????????????????????????
      totalDuration: 100, //????????? ???????????????100
      isUrl: true, //???????????????????????????????????????
      value: 100, //??????
      debace: true, //????????????
      // ??????????????????????????????
      showRightDialog: false,
    };
  },
  mounted() {
    //????????????????????????????????? timeupdate ????????????
    this.musicDurationChange();
    // setTimeout(res => {
    //   console.log(this.getDuration);
    // },5000)
  },
  methods: {
    //?????????????????????
    showRightList() {
      this.showRightDialog = true;
    },
    //??????????????????????????? retuen false ????????????????????? ???????????? .native
    togSingel() {
      if (!this.getNowMusicMenu.id) return false;
      this.$router.push("/singel");
      return false;
    },
    // ???????????????
    nextMusic() {
      //??????????????????????????????
      if (this.getMusicListIds.length === 0) return false;
      // vuex??????????????????
      this.$store.commit(ADD_COUNT);
      //?????????????????????
      this.getMusicUrl(this.getMusicListIds[this.getMusicCount]);
      this.getMusicMenu(this.getMusicListIds[this.getMusicCount]);
      return false;
    },
    preMusic() {
      if (this.getMusicListIds.length === 0) return;
      this.$store.commit(SUB_COUNT);
      this.getMusicUrl(this.getMusicListIds[this.getMusicCount]);
      this.getMusicMenu(this.getMusicListIds[this.getMusicCount]);
      return false;
    },
    getMusicUrl(id) {
      getMusicUrl(id).then((res) => {
        this.$store.commit(NOW_MUSIC, res.data[0].url);
      });
    },
    getMusicMenu(id) {
      getMusicMenu(id).then((res) => {
        this.$store.commit(NOW_MUSICMENU, res.songs[0]);
      });
    },
    volumeChange() {
      let audio = this.$refs.audio;
      audio.volume = this.value / 100;
    },
    musicDurationChange() {
      let audio = this.$refs.audio;
      audio.currentTime = this.musicDuration;
      // this.$store.commit(SET_DURATION,audio.currentTime);
      // audio.currentTime = this.getDuration;
      audio.addEventListener("timeupdate", () => {
        //???????????????????????? example: 257s
        this.totalDuration = audio.duration;
        //??????????????????
        this.musicDuration = audio.currentTime;
        this.$store.commit(SET_DURATION, audio.currentTime);
        // ?????????????????????????????????????????????
        if (audio.currentTime >= audio.duration) {
          if (this.getMusicListIds.length > 0) {
            if (this.debace) {
              this.debace = false;
              this.nextMusic();
              setTimeout((res) => {
                this.debace = true;
              }, 500);
            }
          }
          this.isplay = false;
          this.$store.commit(SET_PLAY, this.isplay);
        }
      });
      // if (this.isplay) return;
      this.isplay = true;
      this.$store.commit(SET_PLAY, this.isplay);
    },
    playMusic() {
      if (this.getNowMusic !== "") {
        if (!this.$refs.audio.paused) {
          this.$refs.audio.pause();
        } else {
          this.$refs.audio.play();
        }
        this.isplay = !this.isplay;
        this.$store.commit(SET_PLAY, this.isplay);
      }
      return false;
    },
  },
  filters: {
    showTime(value) {
      return songTimeFormat(value);
    },
  },
  watch: {
    getNowMusic() {
      this.isplay = true;
      this.$store.commit(SET_PLAY, this.isplay);
      this.isUrl = false;
    },
    getMusicListIds() {
      this.$store.commit(CLEAR_COUNT);
      this.getMusicUrl(this.getMusicListIds[this.getMusicCount]);
      this.getMusicMenu(this.getMusicListIds[this.getMusicCount]);
    },
  },
};
</script>

<style >
/* .el-dialog__wrapper{
  position: absolute !important;
  right: 0;
  bottom: 0;
  z-index: 99999;
  overflow: auto;
} */
::-webkit-scrollbar {
  width: 8px;
}
::-webkit-scrollbar-thumb {
  background-color: rgba(0, 0, 255, 0.1);
  border-radius: 3px;
}
#btmbar .el-dialog{
  position: absolute;
  right: 260px;
  bottom: 80px;
  max-height: 600px;
  overflow: auto;
}
.scroll .el-slider__bar {
  background-image: linear-gradient(to left, #a1c4fd 0%, #c2e9fb 100%);
}
.lound .el-slider__bar {
  background: linear-gradient(#a1c4fd 0%, #c2e9fb 100%);
}
.loundBox:hover .el-slider.is-vertical .el-slider__runway {
  display: block;
}
.lound .el-slider__button {
  width: 10px;
  height: 10px;
}
.lound .el-slider.is-vertical .el-slider__runway {
  width: 4px;
  display: none;
}
</style>
<style scoped>
.lound {
  position: absolute;
  top: -26px;
  left: 10px;
}
.scroll {
  width: 98%;
  position: absolute;
  top: -22px;
  /* left: 217px; */
}
.more {
  display: flex;
  position: relative;
  justify-content: space-around;
  align-items: center;
}
.more-btn {
  padding: 0;
  width: 60px;
  height: 20px;
  background: none;
  color: #888;
  margin-right: 8px;
}
.more div i {
  margin-right: 10px;
  font-size: 16px;
  color: #888;
}
.more div:last-of-type {
  margin-right: 200px;
}
#btmbar {
  position: relative;
  height: 75px;
  background: rgba(0, 0, 255, 0.1);
}
.content {
  display: flex;
  justify-content: space-between;
}
.content .left {
  display: flex;
}
.content .left > div {
  margin-left: 10px;
  margin-top: 10px;
}
.singimg img {
  width: 45px;
  height: 45px;
}
.s-img {
  position: relative;
}
.s-img:hover .m-img {
  display: block;
}
.m-img {
  position: absolute;
  width: 100%;
  height: 100%;
  top: 0;
  left: 0;
  background: rgba(0, 0, 0, 0.5);
  text-align: center;
  line-height: 62px;
  font-size: 20px;
  border-radius: 5px;
  display: none;
}
.sing {
  padding-top: 8px;
  font-size: 14px;
}
.sing-time {
  margin-top: 8px;
  color: rgb(187, 192, 195);
}
.icon {
  padding-top: 15px;
  padding-left: 8px;
  font-size: 20px;
}
.play {
  display: flex;
  justify-content: space-between;
}
.play > div {
  font-size: 40px;
  margin-top: 10px;
}
.play i {
  color: #5192fe;
}
</style>