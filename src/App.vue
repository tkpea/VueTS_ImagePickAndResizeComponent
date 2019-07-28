<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png">
    <div>
    resize: <input type="number" v-model.number="maxLength">px
    </div>
    <div class="item">
      <ImagePickAndResizer 
        v-model="imageFile1"
        :maxLength="maxLength"/>  
      <div v-if="imageFile1">
        {{imageFile1.size | byteNumber}}
        <a v-bind:href="imageFile1 | downloadLink" target="_blank">Download</a>
      </div>
    </div>
    <div class="item">
      <ImagePickAndResizer 
        v-model="imageFile2"
        :maxLength="maxLength"/>  
      <div v-if="imageFile2">
        {{imageFile2.size | byteNumber}}
        <a v-bind:href="imageFile2 | downloadLink" target="_blank">Download</a>
      </div>

    </div>  
  </div>
 </template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';
import ImagePickAndResizer from './components/ImagePickAndResizer.vue';
import {pathToFile} from 'image-to-file-converter';
import * as Numeral from 'numeral'
@Component({
  components: {
    ImagePickAndResizer,
  },
  /** filters */
  filters: {
    byteNumber(value: number): string | null {
      if (!value) {
        return null
      }
      return Numeral(value).format('0 b')
    },
    downloadLink(value: File): string | null {
      if (!value) {
        return null
      }
      return window.URL.createObjectURL(value)
    }
  }  
})
export default class App extends Vue {
  /** Data */
  imageFile1: object? = null
  imageFile2: object? = null
  maxLength: number =  2000

  /**  LifeCycles */
  async beforeCreate() {    
    let image = require('./assets/001.jpg');
    this.imageFile1 = await pathToFile(image)
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.item {
  margin:40px;
}
</style>
