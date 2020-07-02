# el-menu

## Project setup
```
npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```

### Compiles and minifies for production
```
npm run build
```

用原生html5+vue实现一个上传功能
![效果图](https://upload-images.jianshu.io/upload_images/17785488-ec0f9c2c8ab1aa9c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


首先是原生控件 input
```
<input
  type="file"
  accept="image/*"  // 这种可以打开相机或文件，"jpg,png,gif"这种打开只能上传特定文件且没有相机
  @change="changeImage()"
  ref="avatarInput"
  style="display:none"
>
```
隐藏原生上传控件，使用自己上传的图标，并添加element-ui的图片预览功能
```
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
          <!-- 利用element-ui的图片预览插件 -->
          <el-image
            style="width: 100px; height: 100px"
            :src="src"
            :preview-src-list="imgDatas">
          </el-image>
        </div>
      </div>
      <!-- 替换自己的上传图标 -->
      <img class="upload_btn" @click="upLoad" src="./upload.jpg" alt />
    </div>
    
  </div>
</template>
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
```
```
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
```
vue中触发另一个事件
```
upLoad() {
      // 触发上传图片按钮
      this.$refs.avatarInput.dispatchEvent(new MouseEvent("click"));
    }
```
