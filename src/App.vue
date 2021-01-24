<template>
<div>
  <h1>Object Detection Annotation Viewer</h1>
  <h3>Hover over highlighted object to know what part of car it is! </h3>
  <h3>Click on the image to view it in full screen. Press any key to close full screen view </h3>
    <input type="checkbox" id="checkbox" v-model="showWindows">
    <label for="checkbox">Display Windows </label>
    <input type="checkbox" id="checkbox" v-model="showDoors">
    <label for="checkbox">Display Doors </label>
    <input type="checkbox" id="checkbox" v-model="showWheels">
    <label for="checkbox">Display Wheels </label>
  <image-component :checked="checked" :showWindows="showWindows" :showDoors="showDoors" :showWheels="showWheels"></image-component>

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
      showWindows: false,
      showDoors: false,
      showWheels: false,
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
label {
  display: inline-block;
  margin-right: 1em;
}
</style>
