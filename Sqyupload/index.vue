<template>
    <div class="sqy-imgfile">
      <div v-bind:id="uploadId" :title="upTitle">
          <img :src="upInitImg" alt="" class="sqy-imgview" :id="upImgClass"> 
          <svg-icon icon-class="upload"/>
      </div>
      <el-button @click="clickPreview()">预览</el-button>
    </div>
</template>
<script>
import $ from 'jquery'
import WebUploader from 'webuploader'
export default {
    name:'sqyUp',
    props:{
        uploadButton: {
            type: String,
            default: '',
        },
        uploadId:{
          type: String,
          default: '',
        },
        upTitle:{
          type: String,
          default: '',
        },
        upError:{
          type: String,
          default: '',
        },
        upInitImg:{
          type: String,
          default: '',
        },
        upImgClass:{
          type: String,
          default: '',
        },
        upErrorImg:{
          type: String,
          default: '',
        }
    },
    data() {
      return {
          uploader: null,
      }
    },
 mounted() {
      this.initWebUpload();
  },
 methods:{
   initWebUpload() {
      this.uploader = WebUploader.create({
        // swf文件路径
        swf:'/src/styles/Uploader.swf',
        // 文件接收服务端。
        server: '',
        // 选择文件的按钮。可选。
        chunked:true,
        // 内部根据当前运行是创建，可能是input元素，也可能是flash.
        pick: this.uploadButton,
        // 不压缩image, 默认如果是jpeg，文件上传前会压缩一把再上传！
        resize: false,
        accept:{
          title: 'Images',
          extensions: 'jpg,jpeg,bmp,png',
          mimeTypes: 'image/*'
        },
        //是否开启自动上传
        auto:true,
        //
        thisAll:this,
      });
      // 文件上传过程中创建进度条实时显示。
      this.uploader.on( 'fileQueued', function( file) {
        var $li = $(
                '<div id="' + file.id + '" class="sqy-file-item"></div>'
                );
        // $list为容器jQuery实例
        $(this.options.pick).append( $li );
    });
    this.uploader.on( 'uploadProgress', function(file, percentage ) {
      var $li = $( '#'+file.id ),
            $percent = $li.find('.sqy-progress span');
      let percentageFix=percentage.toFixed(2)
        // 避免重复创建
        if ( !$percent.length ) {
            $percent = $('<p class="sqy-progress"><span></span></p>').appendTo( $li ).find('span');
        }
        $percent.css( 'width', percentageFix * 100 + '%' );
        $percent.text(percentageFix * 100 + '% 正在上传中');
    });
    // 文件上传成功，给item添加成功class, 用样式标记上传成功。
    this.uploader.on( 'uploadSuccess', function(file,response ) {
        if(this.options.thisAll.upErrorImg!=""){
          if(file._info.height!="180"){
            this.options.thisAll.$message.error(this.options.errorImg);
          }else{ 
            $(this.options.pick).find("img").attr("src",response.data.webp);
            this.options.thisAll.$message({ type: 'success', message: '上传成功!' });
          }
        }else{
          $(this.options.pick).find("img").attr("src",response.data.webp);
          this.options.thisAll.$message({ type: 'success', message: '上传成功!' });
        }
        
    });
    // 文件上传失败，显示上传出错。
    this.uploader.on( 'uploadError', function( file ) {
       this.options.thisAll.$message.error('上传失败 !');
    });
    // 完成上传完了，成功或者失败，先删除进度条。
    this.uploader.on( 'uploadComplete', function( file ) {
        $( '#'+file.id ).remove();
    });
   },
   clickPreview(){
     let imgurl=$('#'+this.uploadId).find('img').attr("src")
      if(imgurl==""){
         this.$message.error(this.upError)
         return false
      };
     this.$emit("onSuccess",imgurl)
   }
  }
}
</script>
<style>
.sqy-imgfile{
  width: 240px;
  height: 150px;
  display: inline-block;
  position: relative;
}
.sqy-imgfile>div{
  width:155px;
  height: 150px;
  border: 1px solid #ddd;
  position: relative;
  z-index: 3;
  float: left;
}
.webuploader-element-invisible {
	position: absolute !important;
  width: 100%;
  height: 100%;
  opacity: 0;
}
.webuploader-pick{
	position: relative;
	display: inline-block;
	cursor: pointer;
	width:100%;
  height:100%;
}
.webuploader-pick-disable {
	opacity: 0.6;
	pointer-events:none;
}
.sqy-imgfile .el-button{
  float: right;
  position: absolute;
  right: 0;
  bottom: 0;
}
.sqy-imgview{
  width: 100%;
  height: 100%;
  display: inline-block;
  position: absolute;
  left: 0;
  top:0;
}
.sqy-file-item{
  position: fixed;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  z-index: 19999;
  background-color: rgba(255,255,255,0.9);
}
.sqy-progress{
  margin:2vh auto;
  width:500px;
  height:24px;
  background-color: #ebeef5;
  border-radius: 100px;
  white-space: nowrap;
  transition: width .6s ease;
}
.sqy-progress span{
  height:24px;
  line-height: 24px;
  display: inline-block;
  background-color: #67c23a;
  border-radius: 100px;
  text-align: center;
  float: left;
}
.sqy-imgfile .svg-icon{
  width: 11em;
  height: 11em;
  position: absolute;
  top: 0;
  left: 0;
  z-index: -1;
}
</style>
