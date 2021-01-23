<template>
<div>
  <h1>Hello there {{name}}</h1>
  <image-component :circle_diameter="20"></image-component>
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
