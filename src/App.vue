<template>
    <div id="app">
        <h1>El super reproductor de Camila! By DJ MíhGúEL1T0</h1>
        <div></div>
        <input multiple @change="addVideo(this)" ref="filesButton" type="file" accept="video/*"/>
        <button @click="playVideos" >Play videos!</button>
        <video ref="videoPlayer" width="320" height="240" autoplay :src="currentVideo"></video>
        <div>Current Video Duration: {{currentVideoDuration}}</div>
        <div>This video will play for : {{thisVideoWillPlayFor}} seconds</div>
        <div>This video will start at: {{thisVideoWillStartAt}}</div>
        <div>This is the index of the video: {{videoIndex}}</div>
        <div>Difference between duration: {{differenceBetweenDuration}}</div>
        <div>Debug: {{debug}}</div>
        <div v-for="video in videos" v-bind:key="video.id">{{video.file}}</div>
    </div>
</template>

<script>

import fscreen from 'fscreen';

export default {
  name: 'app',
    data: function(){
      return {
          videos: [],
          currentVideo : '',
          currentVideoDuration: 0,
          thisVideoWillPlayFor: 0,
          thisVideoWillStartAt: 0,
          differenceBetweenDuration: 0,
          videoIndex: 0,
          debug: null
      }
    },
    methods: {
        addVideo: function(){
            if(this.$refs.filesButton.files.length === 1){
                this.videos.push({
                    id: Math.random(),
                    file: URL.createObjectURL(this.$refs.filesButton.files[0])
                });
            }else{
                for(let i = 0; i < this.$refs.filesButton.files.length; i++){
                    this.videos.push({
                        id: Math.random(),
                        file: URL.createObjectURL(this.$refs.filesButton.files[i])
                    });
                }
            }
        },

        playVideos: async function(){
            let self = this;
            if (fscreen.fullscreenEnabled) {
                fscreen.addEventListener('fullscreenchange', function handler() {
                    if (fscreen.fullscreenElement !== null) {
                        self.debug ='Entered fullscreen mode';
                    } else {
                        self.debug = 'Exited fullscreen mode';
                    }
                }, false);
                fscreen.requestFullscreen(this.$refs.videoPlayer);
            }
            let keepPlaying = true;
            let index = 0;
            do{
                this.debug = 'comienzo';
                this.videoIndex = parseInt(this.getRandomArbitrary(0, this.videos.length));
                this.currentVideo = this.videos[this.videoIndex].file;
                this.debug = 'video cargado';
                // await this.waitMetadataLoaded(this.$refs.videoPlayer);
                await this.sleep(100);
                this.debug = 'metadata loaded';
                this.currentVideoDuration = this.$refs.videoPlayer.duration;
                this.thisVideoWillStartAt = this.getRandomArbitrary(0.1, this.currentVideoDuration).toFixed(1);
                this.$refs.videoPlayer.currentTime = this.thisVideoWillStartAt;
                this.differenceBetweenDuration = this.currentVideoDuration*1000 - this.thisVideoWillStartAt*1000 ;
                this.thisVideoWillPlayFor = this.getRandomArbitrary(0.1, this.differenceBetweenDuration - 0.2).toFixed(0);
                if(this.thisVideoWillPlayFor < 1000){
                    this.thisVideoWillPlayFor = 1500;
                }else if(this.thisVideoWillPlayFor > 10000){
                    this.thisVideoWillPlayFor = 9900;
                }
                index++;
                this.debug = index;
                await this.sleep(this.thisVideoWillPlayFor);
                this.debug = 'cambie';
            }while(keepPlaying);
        },
        /**
         *
         * @param videoDom = HTML Node
         * @returns {Promise<any>}
         */
        waitMetadataLoaded: function(videoDom){
            return new Promise(resolve => {
                videoDom.addEventListener('loadedmetadata',function(){
                    resolve();
                })
            }, reject => {
                this.debug = 'Rechazado';
                reject();
            })
        },
        sleep: function(ms){
            return new Promise(resolve => setTimeout(resolve, ms));
        },
        getRandomArbitrary: function(min, max) {
            return Math.random() * (max - min) + min;
        }
    }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
