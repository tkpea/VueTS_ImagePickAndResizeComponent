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
      {{imageFile1}}

    </div>
    <div class="item">
    <ImagePickAndResizer 
      v-model="imageFile2"
      :maxLength="maxLength"/>  
      {{imageFile2}}
    </div>  
  </div>
 </template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';
import ImagePickAndResizer from './components/ImagePickAndResizer.vue';
import {pathToFile} from 'image-to-file-converter';
@Component({
  components: {
    ImagePickAndResizer,
  },
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
