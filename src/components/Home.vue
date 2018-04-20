<template lang="html">
  <div class="grid">
    <div class="box a">
      <div class="text-container">
        <div class="message" v-for="msg in messages.length">
          {{messages[msg - 1].msg}}
        </div>
      </div>
    </div>
    <div class="box b">
      <el-switch v-model="isRecording" @change="toggleRecording" title="Record"></el-switch>
      <el-switch v-model="isPlaying" @change="togglePlay" title="Play"></el-switch>
      <audio  ref="audio"
          preload="auto"
          v-model="dataUrl"
        >
      </audio>
      <el-card style="background-color: #323232;">
        <el-input
          type="textarea"
          :rows="4"
          v-model="msg"
          >
        </el-input>
        <el-button class="btn" @click="send">Send</el-button>
      </el-card>
    </div>
    <div class="box c">C</div>
    <div class="box d">D</div>
  </div>
</template>

<script>
export default {
  data(){
    return {
      isRecording: false,
      audioRecorder: null,
      recordingData: [],
      isPlaying: false,
      messages: [{
        msg:"Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.",
        dark: false
      }],

      msg: '',
      dataUrl:'',
    }
  },

  methods: {
    send() {
      this.messages.push({
        msg: this.msg,
        dark:false
      });
      this.msg=''
    },

    toggleRecording(){
      var that = this;
      if (this.isRecording){
        navigator.getUserMedia =  navigator.webkitGetUserMedia || navigator.getmozGetUserMedia || navigator.getUserMedia;
        navigator.getUserMedia({
          audio: true,
          video: false
        }, function(stream){
          that.stream = stream;
          that.audioRecorder = new MediaRecorder(stream);
          that.audioRecorder.start();
          console.log('Media recorder started');
        }, function(error){
          alert(JSON.stringify(error));
        });
      } else {
        this.audioRecorder.stop();

      }
      this.audioRecorder.ondataavailable = function(event){
        console.log('Recording...');
        that.recordingData.push(event.data);
      }

      this.audioRecorder.onstop = function(event) {
        console.log('Media recorder stopped');

        var blob = new Blob(that.recordingData, {
          type: 'audio/wav',
        });

        that.dataUrl = window.URL.createObjectURL(blob);
      }


    },

    togglePlay(){
      if(this.$refs.audio.paused=== false){
        this.$refs.audio.pause();

      } else {
        this.$refs.audio.play();
      }
    },

    removeRecording() {

    },

    submitRecording(){

    }
  },
}
</script>

<style lang="scss">

.grid {
  display: grid;
  grid-gap: 10px;
  grid-template-columns:  repeat(4, 1fr);
  grid-template-rows: repeat(4, 1fr);
  background-color:black; // #fff;
  color: #444;
  //padding: 10px;
  height: 100%;
}

.box {
background-color: #444;
  color: #fff;
//  border-radius: 5px;
  padding: 20px;
  font-size: 150%;

}

.a {
  grid-column: 1 /span 3;
  grid-row: 1 / span 3;
  overflow-y: auto;
}

.b {
  grid-column: 1 / span 3;
  //grid-row: span 2;
}

.c {
  grid-row: 1 / span 2;

}

.d {
  grid-row: 3 / span 2;
}

.rec {
  margin: 0 0 10px;
  border-radius: 50px;
}

.btn {
  margin: 15px;
  color: white;
  background-color: #232323;
}

.message {
  border:2px solid #dedede;
  background-color: #f1f1f1;
  border-radius: 5px;
  padding: 10px;
  margin: 10px 0;
  color: black;
}

.darker {
  border-color: #ccc;
  background-color: #ddd;
}
</style>
