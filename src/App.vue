<template>
  <div id="app">
    <header>
      <h1>📻.protocode_</h1>
    </header>
    <main>
      <div class="protocode"></div>
      <section class="player">
        <audio ref="player" @timeupdate="updateProgressBar">
          Your browser does not support the
          <code>audio</code> element.
        </audio>
        <!-- Progress -->
        <div class="player-container">
          <button class="play" v-if="!isPlaying" @click="play"><i class="fa fa-play" aria-hidden="true"></i></button>
          <button class="pause" v-else @click="pause"><i class="fa fa-pause" aria-hidden="true"></i></button>
          <div class="controls-container">
            <div
              class="progress-container"
              id="progress-container"
              ref="progressContainer"
              @click="seek"
            >
              <div
                class="progress"
                id="progress"
                :style="{ width: progress + '%' }"
              ></div>
              <div class="duration-wrapper">
                <span>{{ currentTime }}</span>
                <!-- Title / Info -->
                <span class="song-title">
                  {{ current.title }} - <span>{{ current.artist }}</span>
                </span>
                <span>{{ duration }}</span>
              </div>
            </div>
            <!-- Controls -->
            <div class="controls">
              <button class="prev" @click="prev">Prev</button>
              <button class="next" @click="next">Next</button>
            </div>
          </div>
        </div>
      </section>
      <!-- Playlist -->
      <!-- <section class="playlist">
        <h3>The Playlist</h3>
        <button
          v-for="song in songs"
          :key="song.src"
          @click="play(song)"
          :class="song.src == current.src ? 'song playing' : 'song'"
        >
          {{ song.title }} - {{ song.artist }}
        </button>
      </section> -->
    </main>
    <!--<HelloWorld msg="Welcome to Your Vue.js App" />-->
  </div>
</template>

<script>
// import HelloWorld from "./components/HelloWorld.vue";

export default {
  name: "App",
  components: {
    // HelloWorld,
  },
  data() {
    return {
      current: {},
      index: 0,
      isPlaying: false,
      progress: 0,
      duration: 0,
      currentTime: 0,
      songs: [
        {
          title: "Grateful",
          artist: "Neffex",
          src: require("./assets/neffex-grateful.mp3"),
        },
        {
          title: "Invincible",
          artist: "Deaf Kev",
          src: require("./assets/deaf-kev-invincible.mp3"),
        },
      ],
    };
  },
  methods: {
    play(song) {
      if (typeof song.src != "undefined") {
        this.current = song;

        this.$refs.player.src = this.current.src;
      }

      this.$refs.player.play();
      this.$refs.player.addEventListener("ended", this.next);
      this.isPlaying = true;
    },
    pause() {
      this.$refs.player.pause();
      this.isPlaying = false;
    },
    next() {
      this.index++;
      if (this.index > this.songs.length - 1) {
        this.index = 0;
      }

      this.current = this.songs[this.index];
      this.play(this.current);
    },
    prev() {
      this.index--;
      if (this.index < 0) {
        this.index = this.songs.length - 1;
      }

      this.current = this.songs[this.index];

      this.play(this.current);
    },
    updateProgressBar(e) {
      if (this.isPlaying) {
        const { duration, currentTime } = e.srcElement;
        // update the progress
        this.progress = (currentTime / duration) * 100;
        // calculate display for duration
        let durationMinutes = Math.floor(duration / 60);
        let durationSeconds = Math.floor(duration % 60);
        if (durationSeconds < 10) {
          durationSeconds = `0${durationSeconds}`;
        }

        // delay switching duration to avoid NaN displayed
        if (durationSeconds) {
          this.duration = `${durationMinutes}:${durationSeconds}`;
        }

        // calculate display for currentTime
        let currentMinutes = Math.floor(currentTime / 60);
        let currentSeconds = Math.floor(currentTime % 60);
        if (currentSeconds < 10) {
          currentSeconds = `0${currentSeconds}`;
        }
        this.currentTime = `${currentMinutes}:${currentSeconds}`;
      }
    },
    seek(e) {
      const width = this.$refs.progressContainer.clientWidth;
      console.log("width", width);
      const clickX = e.offsetX;
      const { duration } = this.$refs.player;
      console.log("clickX", clickX);
      this.$refs.player.currentTime = (clickX / width) * duration;
      this.$refs.player.play();
      this.$refs.player.addEventListener("ended", this.next);
      this.isPlaying = true;

    },
  },
  mounted() {
    this.current = this.songs[this.index];
    this.$refs.player.src = this.current.src;
  },
};
</script>

<style lang="scss">
#app {
  text-align: center;
}

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
body {
  font-family: sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  height: 100vh;
  background: rgb(5, 7, 17);
  color: #fff;
}
img {
  width: 100%;
}
song {
  color: f3f3f3;
}
header {
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 15px;
  color: #fff;
}

main {
  width: 100%;
  margin: 0 auto;
}

.protocode {
  height: 78vh;
  background: url('./assets/protocode.jpg') no-repeat center;
}

.player-container {
  display: flex;
  margin: 10px 0px;
}

.controls-container {
  width: 100%;
  margin: 0 5px;
}
.controls {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-top: -25px;
}
button {
  appearance: none;
  background: none;
  border: none;
  outline: none;
  cursor: pointer;
  background: rgb(12, 15, 39);
}
button:hover {
  opacity: 0.8;
}
.play,
.pause {
  font-size: 20px;
  font-weight: 700;
  padding: 15px 30px;
  margin-left: 5px;
  color: #fff;
  //background-image: url();
}
.next,
.prev {
  font-size: 16px;
  font-weight: 700;
  padding: 10px 20px;
  color: #fff;
}

/* Progress */
.duration-wrapper {
  position: relative;
  top: -25px;
  display: flex;
  justify-content: space-between;
  pointer-events: none;
  color: #616a75;
}
.song-title {
  font-size: 15px;
  text-transform: uppercase;
  text-align: center;
}
.song-title span {
  // font-weight: 400;
}
.progress-container {
  background: rgb(63, 63, 63);
  border-radius: 5px;
  cursor: pointer;
  margin-top: 30px;
  margin-bottom: 30px;
  height: 4px;
}

.progress {
  background: #b6b6b6;
  border-radius: 5px;
  height: 100%;
  width: 10%;
  transition: width 0.1s linear;
  z-index: 1;
}

/* Playlist */
.playlist {
  padding: 0px 30px;
}
.playlist h3 {
  color: #f3f3f3;
  font-size: 28px;
  font-weight: 400;
  margin-top: 30px;
  margin-bottom: 30px;
  text-align: center;
}
.playlist .song {
  display: block;
  width: 100%;
  padding: 15px;
  font-size: 20px;
  font-weight: 700;
  cursor: pointer;
  color: #fff;
}
.playlist .song:hover {
  color: #6dfefb;
}
.playlist .song.playing {
  color: #fff;
  border: 1px solid #6dfefb;
}
</style>
