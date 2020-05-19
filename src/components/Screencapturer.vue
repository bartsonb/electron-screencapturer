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
  },
  methods: {
    getVideoSources() {

    }, 
    setVideoStream() {

    }, 
    startRecording() {

    }, 
    stopRecording() {

    },
    handleDataAvailable() {

    }, 
    async handleDataStop() {

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
