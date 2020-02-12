<template>
  <div id="app">
    <img src="./assets/logo.png" />
    <div v-if="images.length > 0">
      <button @click="uploadImages">Upload Seleted Images</button>
    </div>
    <hr/>
    <VueUploadImages
      ref="uploadImage"
      :max-image="maxImage"
      :data-images="images"
      @update-image="updateImage"
    />
  </div>
</template>

<script>
import VueUploadImages from "./components/VueUploadImages";

export default {
  name: "App",
  components: {
    VueUploadImages
  },
  data() {
    return {
      isPosting: false,
      commentText: null,
      maxImage: 4,
      images: []
    };
  },
  methods: {
    /**
     * 画像配列更新
     */
    updateImage(images) {
      this.images = images;
    },
    uploadImages() {
      console.log("Upload Event");
    }
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
