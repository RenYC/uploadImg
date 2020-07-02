<template>
  <div>
    <input
      type="file"
      accept="image/*"
      @change="changeImage()"
      ref="avatarInput"
      style="display:none"
    />
    <div class="pic_list_box">
      <div class="pic_list" v-show="imgDatas.length">
        <div v-for="(src,index) in imgDatas" :key="index">
          <!-- <img :src="src" width="80" height="80" alt srcset /> -->
          <el-image
            style="width: 100px; height: 100px"
            :src="src"
            :preview-src-list="imgDatas">
          </el-image>
        </div>
      </div>
      <img class="upload_btn" @click="upLoad" src="./upload.jpg" alt />
    </div>
    
  </div>
</template>

<script>
import 'element-ui/lib/theme-chalk/index.css';

export default {
  data() {
    return {
      imgDatas: []
    }
  },
  methods: {
    changeImage() {
      // 上传图片事件
      var files = this.$refs.avatarInput.files;
      var that = this;
      function readAndPreview(file) {        
        //Make sure `file.name` matches our extensions criteria
        if (/\.(jpe?g|png|gif)$/i.test(file.name)) {
          var reader = new FileReader();
          reader.onload = function(e) {
            // 防止重复上传
            if (that.imgDatas.indexOf(e.target.result) === -1) {
              that.imgDatas.push(e.target.result);
            }
          };
          reader.readAsDataURL(file);
        }
      }
      readAndPreview(files[0])
      if (files.length === 0) {
        return;
      }
      
      // 文件上传服务器
      // this.setUploadFile(files[0])
      
    },
    setUploadFile(file) {
      this.formData = new FormData()
      this.formData.append('files', file, file.name) // 添加到请求体
      this.$http
        .post('/api/dxbase/upload?resType=EVENT', this.formData)
        .then(res=> {
          console.log(res);
        })
    },
    upLoad() {
      // 触发上传图片按钮
      this.$refs.avatarInput.dispatchEvent(new MouseEvent("click"));
    }
  }
};
</script>

<style>
.pic_list_box {
  display: flex;
}
.upload_btn {
  width: 100px;
  height: 100px;
  padding-left: 15px;
}
.pic_list {
  display: flex;
}
</style>