<template>
  <div id="app">
    <HelloWorld msg="vue + pixi + dragonbones"/>
    <pixi-renderer :w="800" :h="2000">
      <pixi-container
        :x="200" :y="200"
        msg="pixi container"
      >
        <pixi-sprite :x="400" :y="600" />
        <pixi-sheep :x="400" :y="1200" /> 
        <pixi-dog :x="400" :y="1600" /> 
      </pixi-container>
    </pixi-renderer>
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue';
import PixiRenderer from './pixi/PixiRenderer.vue';
import PixiSprite from './pixi/PixiSprite.vue';
import PixiSheep from './pixi/PixiSheep.vue';
import PixiDog from './pixi/PixiDog.vue';
import PixiContainer from './pixi/PixiContainer.vue';

export default {
  data() {
    return {
      pixiWidth: 800,
      pixiHeight: 600
    }
  },

  components: {
    HelloWorld,
    PixiRenderer,
    PixiSprite,
    PixiSheep,
    PixiDog,
    PixiContainer
  },

   methods: {
    // tickInfo(container, delta) {
    //   console.log(`Tick delta: ${delta}`)
    // }
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
   position: absolute;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
}
</style>
