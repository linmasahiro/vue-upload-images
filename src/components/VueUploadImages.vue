<!-- VueUploadImages Component -->
<template>
  <div>
    <div v-if="images.length < maxImage" class="cursor-pointer upload-btn" @click="addImages">
      <div>
        <label class="cursor-pointer">{{ selectText }}({{ images.length }}/{{ maxImage }})</label>
      </div>
    </div>
    <div v-for="(image, index) in images" :key="index" class="full-width img-box">
      <div>
        <img class="img img-responsive" :src="image" />
      </div>
      <div class="image-bottom full-width align-items-center justify-content-between">
        <div>
          <label
            class="image-edit display-flex cursor-pointer"
            @click="editImage(index)"
          >{{ editText }}</label>
          <label
            class="image-delete display-flex cursor-pointer"
            @click="deleteImage(index)"
          >{{ deleteText }}</label>
        </div>
      </div>
    </div>
    <input
      type="file"
      style="display: none;"
      ref="addFiles"
      :multiple="multiple"
      :accept="accept"
      @change="onImageAdd"
    />
    <input type="file" style="display: none;" ref="editFile" :accept="accept" @change="onImageEdit" />
  </div>
</template>

<script>
export default {
  name: "VueUploadImages",
  props: {
    // 選択ボタン文字列
    selectText: {
      type: String,
      default: "Select Images"
    },
    // アップロードボタン文字列
    uploadText: {
      type: String,
      default: "Upload Images"
    },
    // 変更ボタン文字列
    editText: {
      type: String,
      default: "Edit"
    },
    // 削除ボタン文字列
    deleteText: {
      type: String,
      default: "Delete"
    },
    // 制御MIME
    accept: {
      type: String,
      default: "image/gif,image/jpeg,image/png,image/bmp,image/jpg"
    },
    // 画像
    dataImages: {
      type: Array,
      default: () => {
        return [];
      }
    },
    // 複数可否
    multiple: {
      type: Boolean,
      default: true
    },
    // 上限
    maxImage: {
      type: Number,
      default: 4
    }
  },
  data() {
    return {
      // 選択中インデックス
      currentIndexImage: 0,
      // 画像
      images: []
    };
  },
  methods: {
    /**
     * 画像追加押下
     */
    addImages() {
      this.$refs.addFiles.click();
    },
    /**
     * 画像追加
     *
     * @params Object e エレメント
     */
    onImageAdd(e) {
      const images = e.target.files || e.dataTransfer.files;
      for (var i = 0; i < images.length; i++) {
        if (i <= this.maxImage) {
          this.getBase64(images[i])
            .then(image => {
              this.images.push(image);
            })
            .catch(error =>
              console.log(error)
            );
        }
      }
      e.target.value = "";
      this.updateFatherCompomentImages();
    },
    /**
     * 画像変更
     *
     * @params Object e エレメント
     */
    onImageEdit(e) {
      const images = e.target.files || e.dataTransfer.files;
      this.getBase64(images[0])
        .then(image => {
          let tmp = this.images;
          tmp[this.currentIndex] = image;
          this.images = [];
          this.images = tmp;
        })
        .catch(error =>
          console.log(error)
        );
      e.target.value = "";
      this.updateFatherCompomentImages();
    },
    /**
     * 画像をbase64へエンコード
     *
     * @params Object file 画像
     */
    getBase64(file) {
      return new Promise((resolve, reject) => {
        const reader = new FileReader();
        reader.readAsDataURL(file);
        reader.onload = () => resolve(reader.result);
        reader.onerror = error => reject(error);
      });
    },
    /**
     * 画像変更押下
     *
     * @params int currentIndex
     */
    editImage(currentIndex) {
      this.currentIndex = currentIndex;
      this.$refs.editFile.click();
    },
    /**
     * 画像削除押下
     *
     * @params int currentIndex
     */
    deleteImage(currentIndex) {
      this.images.splice(currentIndex, 1);
      this.currentIndex = null;
      this.updateFatherCompomentImages();
    },
    /**
     * 親画像配列を更新する
     */
    updateFatherCompomentImages() {
      this.$emit("update-image", this.images);
    },
    /**
     * 画像をクリアする
     */
    clearImages() {
      this.images = [];
    }
  },
  created() {
    this.images = [];
    this.images = this.dataImages;
  }
};
</script>

<style scoped>
.upload-btn {
  border: 1px solid black;
  width: 50%;
  margin: 0 auto;
}
.upload-btn:hover {
  background: green;
  color: aliceblue;
}
.cursor-pointer {
  cursor: pointer;
}
.img-responsive {
  display: block;
  max-width: 80%;
  height: auto;
  margin: 0 auto;
}
.img-box {
  border: 1px solid black;
  margin: 5px;
}
.img {
  max-height: 300px;
  max-width: 100%;
  display: block;
}
.image-bottom {
  bottom: 0;
  left: 0;
  height: 40px;
  padding: 5px 10px;
  box-sizing: border-box;
}
.full-width {
  width: 100%;
}
.image-edit {
  margin-right: 10px;
}
</style>