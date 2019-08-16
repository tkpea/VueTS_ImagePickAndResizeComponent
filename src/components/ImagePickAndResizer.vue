<template>
  <div class="wrap">

    <div class="image-picker">
      <div
        class="droparea"
        @dragover.prevent="onDrag($event, 'new', true)"
        @dragleave.prevent="onDrag($event, 'new', false)"
        @drop.prevent="onFileDrop"
        v-bind:class="{ 'is-drag': isDragOver }"
      >
        <label class="file-label">
          <span class="btn">画像を選択</span>
          <input
            type="file"
            v-on:change="onFileChange"
            class="hidden"
            accept="image/*"
          />
        </label>
      </div>
      <div class="preview" v-if="value">
        <img :src="previewSrc" v-if="!previewLoading"/>
        <span class="remove-button" @click="removeImage"></span>
      </div>
      </div>
     
  </div>
</template>
<script lang="ts">
import { Component, Prop, Vue, Watch } from 'vue-property-decorator';
import { base64ToFile } from 'image-to-file-converter';

@Component({
})
export default class ImagePicker extends Vue {
  
  /** Props */
  @Prop({ type: Object})
  value: Object

  @Prop({ type: Number, default: null })
  maxLength: Number

  /** Data */
  isDragOver: boolean = false
  previewSrc: string? =""
  previewLoading: boolean = false

 /** Watch */
  @Watch('value')
  onValueChange(newValue: string, oldValue: string): void {
  
    let reader = new FileReader()
    reader.addEventListener(
      'load',
      function(event: object) {
        this.previewSrc = event.target.result
        this.previewLoading = false

      }.bind(this)
    )
    if(this.value){
      this.previewLoading = true
      reader.readAsDataURL(this.value);
    }
  }

   /**  LifeCycles */
  mounted() { 
  }

  /** Event Methods */
  onDrag (event: object, key: string, status:boolean) {
    this.isDragOver = status
  }
  
  onFileDrop(e: object): void{
    this.createImage(e.dataTransfer.files[0])
  }
  
  onFileChange(e: object){
    this.createImage(e.target.files[0])
  }

  /** Methods */
  createImage(file: File){
    
    if (!file.type.match('image')){
      this.isDragOver = false
      return;
    } 
   
    let reader = new FileReader()
      reader.addEventListener(
      'load',
      async function(event: object) {
        try {
          const resizeImageFile = await base64ToFile(event.target.result, this.maxLength)
          this.$emit('input', resizeImageFile)     
          this.isDragOver = false  
        } catch (error) {
          console.error(error)
          this.isDragOver = false
        }        
      }.bind(this)
      )
      reader.readAsDataURL(file)
  }
  removeImage(){
    this.$emit('input', null)
    this.previewSrc = null
  }
}

</script>
<style lang="scss" scoped>
.wrap {
  display: flex;
  align-items: center;
  justify-content: space-between;
}
.hidden {
  display: none;
}
.image-picker {
  color: #777;
  width: 100%;
  position: relative;
  .droparea {
    &.is-drag {
      opacity: 0.6;
      transition: 0.4s;
    }
    background: #f1f1f1;
    border: dashed 2px #ccc;
    .notice {
      font-size: 12px;
      line-height: 1em;
    }
    .file-label {
      width: 100%;
      cursor: pointer;
      text-align: center;
      height: 150px;
      display: flex;
      align-items: inherit;
      flex-direction: column;
      justify-content: center;
      i {
        font-size: 32px;
        color: #999;
      }
      .btn {
        background: #3f51b5;
        color: #fff;
        padding: 5px 10px;
        margin: 10px auto;
        box-shadow: 0 2px 5px rgba(0, 0, 0, 0.5);
        &:hover {
          opacity: 0.8;
          transition: 0.2s;
        }
      }
    }
  }
  .preview {
    position: absolute;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
    overflow: hidden;
    background: #f1f1f1;
    img {
      border-style: none;
      height: 100%;
      width: 100%;
      object-fit: contain;
    }
    &:hover {
      .remove-preview-button {
        opacity: 1;
        display: block;
      }
    }
  }
}
.preview {
  text-align: center;
  width: 100%;

  img {
    border-style: none;
    object-fit: contain;
    width: 100%;
    height: 100%; 
  }

  .remove-button {
    position: absolute;
    right: 10px;
    top: 10px;
    width: 32px;
    height: 32px;
    opacity: 0.3;
    cursor: pointer;
    &:hover {
       opacity: 1; 
    }
    &::before,
    &::after {
      position: absolute;
      left: 15px;
      content: ' ';
      height: 33px;
      width: 2px;
      background-color: #333;     
    }
    &::before {
     transform: rotate(45deg);  
    }
    &::after {
    transform: rotate(-45deg);    
    }
  }
}
.remove-preview-button {

}
</style>
