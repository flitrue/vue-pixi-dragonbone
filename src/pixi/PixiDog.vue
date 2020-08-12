<template>
  <div class="pixi-dog">
  </div>
</template>


<script>
export default {
  
  inject: ['EventBus', 'PIXIWrapper', 'DragonBones'],
  props: ["x", "y"],
  // x, y define the sprite's position in the parxwent.
  // imagePath is the path to the image on the server to render as the sprite.

  data() {
    return {
      target: null,
      armatureDisplay: null,
      scale: 1,
      animationNames: [],
    }
  },

  created() {
    this.EventBus.$on('sheepLoaded', () => {
      console.log(this);
        this.loadDog();
    });
  },

  methods: {
    loadDog(){
      this.target = new this.PIXIWrapper.PIXI.Point();

      this.animationNames = [
        'look_X',
        'look_Y'
      ];

      this.PIXIWrapper.PIXI.Loader.shared
      .add('dog_ske', 'dog/dog_ske.json')
      .add('dog_tex_json', 'dog/dog_tex.json')
      .add('dog_tex', 'dog/dog_tex.png')
      .load(this.onAssetsLoaded);
    },

    onAssetsLoaded(loader, res){
      let factory = this.DragonBones.PixiFactory.factory;

      factory.parseDragonBonesData(res.dog_ske.data);
      factory.parseTextureAtlasData(res.dog_tex_json.data, res.dog_tex.texture);

      console.log(factory); 

      this.armatureDisplay = factory.buildArmatureDisplay('Dog');
      this.armatureDisplay.x = this.x;
      this.armatureDisplay.y = this.y;
      this.armatureDisplay.scale.set(this.scale, this.scale);
      this.PIXIWrapper.PIXIApp.stage.addChild(this.armatureDisplay);

      console.log(this.armatureDisplay);

      this.PIXIWrapper.PIXIApp.stage.interactive = true;
      this.PIXIWrapper.PIXIApp.stage.on('touchmove', this.moveHandler);
      this.PIXIWrapper.PIXIApp.stage.on('mousemove', this.moveHandler);

      //this.PIXIWrapper.PIXIApp.stage.on('mousedown', this.touchHandler);
      this.PIXIWrapper.PIXIApp.start();
      this.PIXIWrapper.PIXI.Ticker.shared.add(this.enterFrameHandler);

    },


    moveHandler(event) {
        const x = event.data.global.x;
        const y = event.data.global.y;

        this.target.x += ((x - this.PIXIWrapper.PIXIApp.stage.x - this.armatureDisplay.x) / 0.4 - this.target.x) * 0.3;
        this.target.y += ((y - this.PIXIWrapper.PIXIApp.stage.y - this.armatureDisplay.y) / this.scale - this.target.y) * 0.3;
    },

    enterFrameHandler() {
      const armature = this.armatureDisplay.armature;
      const animation = this.armatureDisplay.animation;
      const canvas = armature.armatureData.canvas;

      let p = 0.0;
      const pX = Math.max(Math.min((this.target.x - canvas.x) / (canvas.width * 0.5), 1.0), -1.0);
      const pY = -Math.max(Math.min((this.target.y - canvas.y) / (canvas.height * 0.5), 1.0), -1.0);

      if(!this.onTask){
        for (const animationName of this.animationNames) {
            if (animation.hasAnimation(animationName)) {
                let animationState = animation.getState(animationName, 1);
                if (!animationState) {
                    animationState = animation.fadeIn(animationName, 0.1, 1, 1, animationName);
                    if (animationState) {
                        animationState.resetToPose = false;
                        animationState.stop();
                    }
                }

                if (animationState) {
                    switch (animationName) {
                        case 'look_X':
                            p = (pX + 1.0) * 0.5;
                            break;

                        case 'look_Y':
                            p = (pY + 1.0) * 0.5;
                            break;
                        
                        default:
                            break;
                    }
                    animationState.currentTime = p * animationState.totalTime;
                  }

            }
            } 
    } else {
      if(animation.isCompleted) this.onTask = false;
    }
    }
}
}
</script>