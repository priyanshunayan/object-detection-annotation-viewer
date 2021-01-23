<template>
<div>
  <h1>Object Detection Annotation Viewer</h1>
  <h3>Hover over highlighted object to know what part of car it is! </h3>
  <h3> Double Click on the image to see it in full screen. Press any key to close full screen view </h3>
    <input type="checkbox" id="checkbox" v-model="checked">
    <label for="checkbox">Toogle Labels from here</label>
  <image-component :checked="checked"></image-component>

</div>
</template>

<script>
import ImageComponent from "./components/ImageComponent"
export default {
  name: 'App',
  components: {
    ImageComponent
  },
  data(){
    return {
      checked: true,
      name: "Priyanshu Nayan"
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
