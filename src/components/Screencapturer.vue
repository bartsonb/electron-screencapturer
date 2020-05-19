<template>
  <div class="screencapturer">
    <h1>Screencapturer</h1>

    <video ref="videoElement" :class="{ isRecording: recording }"></video>

    <p class="capturedElement">Video Source: <span>{{ (source !== null) ? source.name : 'None' }}</span></p>
  
    <div class="controls">

      <button 
        class="button btnStart" 
        @click="startRecording"
        :disabled="(mediaRecorder === null && recording === false) || recording === true">Start</button>

      <button   
        class="button btnStop" 
        @click="stopRecording" 
        :disabled="recording === false">Stop</button>

      <button 
        class="button btnSelect" 
        @click="getVideoSources"
        :disabled="recording === true">Select video source</button>

    </div>
  </div>
</template>

<script>
const { desktopCapturer, remote, shell, ipcRenderer } = require('electron');
const fs = require('fs');
const { Menu } = remote;

export default {
  name: 'Screencapturer', 
  data() {
    return {
      source: null,
      mediaRecorder: null,
      recording: false,
      recordedChunks: []
    }
  },
  mounted() {
    ipcRenderer.on('shortcutPressed', (e, shortcut) => {
      switch(shortcut) {
        case 'Shift+j': 
          this.startRecording();
          break;
        case 'Shift+k':
          this.stopRecording();
          break;
        case 'Shift+l':
          this.getVideoSources();
          break;
      }
    })
  },
  methods: {
    getVideoSources() {

      desktopCapturer.getSources({ types: ['window', 'screen'] })
        .then(sources => {

          Menu.buildFromTemplate(
            sources.map(source => ({
              label: source.name,
              click: () => {
                this.source = source;
                this.setVideoStream();
              }
            }))
          ).popup();

        })
        .catch(err => {
          console.log(err);
        });

    }, 
    setVideoStream() {
      const contraints = {
        audio: false,
        video: {
          mandatory:  {
            chromeMediaSource: 'desktop', 
            chromeMediaSourceId: this.source.id
          }
        }
      }

      navigator.mediaDevices.getUserMedia(contraints)
        .then(mediaStream => {
          this.$refs.videoElement.srcObject = mediaStream;
          this.$refs.videoElement.play();

          this.mediaRecorder = new MediaRecorder(mediaStream, { mimeType: 'video/webm; codecs=vp9' });

          this.mediaRecorder.ondataavailable = this.handleDataAvailable;
          this.mediaRecorder.onstop = this.handleDataStop;
        })
    }, 
    startRecording() {
      this.mediaRecorder.start();
      this.recording = true;
    }, 
    stopRecording() {
      this.mediaRecorder.stop();
      this.recording = false;

      this.recordedChunks = [];
    },
    handleDataAvailable(e) {
      this.recordedChunks.push(e.data);
    }, 
    async handleDataStop() {
      const blob = new Blob(this.recordedChunks, {
        type: 'video/webm; codecs=vp9'
      });

      const buffer = Buffer.from(await blob.arrayBuffer())
      const userData = remote.app.getPath('userData');
      const filePath = `${userData}/videos`;

      fs.mkdir(filePath, () => {
        fs.writeFile(`${filePath}/vid-${Date.now()}.webm`, buffer, () => {
          console.log('Wrote to file.')

          const notifcation = new Notification('Screencapturer', {
            body: `File saved to: ${userData}/videos/vid-${Date.now()}.webm`
          })

          notifcation.onclick = () => {
            shell.openItem(remote.app.getPath('userData') + '/videos');
          }

          this.$root.$emit('savedVideo');
        });
      });
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
  .screencapturer {
    display: flex;
    flex-direction: column;
    margin-bottom: 15px;
    border-radius: 4px;
    padding: 15px 35px;

    > h1 {
      padding: 0 0 10px 0;
    }
  }

  video {
    width: 100%;
    background: #000;
    border-radius: 4px;
    max-height: 500px;
    border: solid 3px transparent;
    transition: all 0.3s ease-in-out;
  }

  .isRecording {
    border-color: #f14668;
  }

  .capturedElement {
    font-size: 12px;
    padding: 5px 0;
    text-transform: uppercase;

    span {
      font-weight: bold;
    }
  }

  .controls {
    display: flex;
    flex-direction: row;
    justify-content: center;
    flex-grow: 1;
    margin-top: 10px;

    > button {
      width: 100%;
      padding: 12px 12px;
      border-radius: 3px;
      transition: all 0.1s ease-in-out;
      border-color: transparent;
      color: #fff;
      cursor: pointer;

      &.btnSelect { 
        background-color: #3273dc; 

        &:hover:enabled {
          background-color: #2363ca;
        }  
      }     

      &.btnStart { 
        background-color: #48c774; 

        &:hover:enabled { background-color: #3ba05e; }
      }

      &.btnStop { 
        background-color: #f14668; 

        &:hover:enabled { background-color: #be3652; }
      }
      
      &:disabled {
        background-color: grey;
        cursor: default;
      }

      &:not(:last-child) {
        margin-right: 10px;
      }
    }
  }

</style>
