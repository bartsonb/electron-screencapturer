<template>
  <div class="gallery">
    <h1>Videos <span>{{ (videos.length > 0) ? `${videos.length}` : '' }}</span></h1>
    
    <div class="videos">
      <p v-if="videos.length === 0">No videos available.</p>

      <div class="video" v-for="(video, index) in videos" :key="index">
        {{ video }}
      </div>

      <button class="btnOpenFolder" @click="openFolder">Open folder</button>
    </div>


  </div>
</template>

<script>
const { remote, shell } = require('electron')
const fs = require('fs')

export default {
  name: 'Gallery', 
  data: () => ({
    videos: [], 
    thumbnails: []
  }),
  methods: {
    getVideoFiles() {
      this.videos = [];

      const _this = this;
      const filePath = remote.app.getPath('userData') + '/videos';

      fs.readdir(filePath, (err, files) => {
        _this.videos.push(...files);
      });
    }, 
    openFolder() {
      shell.openItem(remote.app.getPath('userData') + '/videos');
    }
  }, 
  mounted() {

    this.$root.$on('savedVideo', () => {
      this.getVideoFiles();
    })
  },
  created() {
    this.getVideoFiles()
  }
}

</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
  .gallery {
    display: flex;
    flex-direction: column;
    margin-bottom: 15px;
    border-radius: 4px;
    padding: 15px 35px;

    > h1 {
      padding: 0 0 10px 0;
      align-items: flex-start;
      display: flex;

      > span {
        background: #3273dc;
        border-radius: 300px;
        padding: 2px 6px;
        margin-left: 4px;
        color: #fff;
        font-weight: 100;
        font-size: 14px;
        top: 0;
        display: inline-block;
      }
    }

    .btnOpenFolder {
      width: 100%;
      padding: 12px 12px;
      margin-top: 20px;
      border-radius: 3px;
      transition: all 0.1s ease-in-out;
      border-color: transparent;
      background-color: #3273dc; 
      color: #fff;

      &:hover {
        background-color: #2363ca;
      }  
    }
  }
</style>
