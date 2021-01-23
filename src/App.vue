<template>
<div>
  <h1>Object Detection</h1>
  <p>Hover over highlighted object to know what part of car it is! </p>
  <p> Click on the image to see it full screen. Press any key to close </p>
  <image-component></image-component>
  <input type="checkbox" id="checkbox" v-model="checked">
<label for="checkbox">{{ checked }}</label>
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
