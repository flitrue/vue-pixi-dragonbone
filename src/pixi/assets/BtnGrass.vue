<script>
export default {
	inject: ["EventBus", "PIXIWrapper"],
	// x, y define the sprite's position in the parent.
	// imagePath is the path to the image on the server to render as the sprite.
	props: ["x", "y", "mousedown"],

	data() {
		return {
			sprite: null,
			scale: 0.3
		};
	},

	render(h) {
		return h();
	},

	created() {
        this.sprite = this.PIXIWrapper.PIXI.Sprite.from("sheep2/grass.png");
        this.sprite.scale.set(this.scale, this.scale);

		// Forward the pointerdown event.
		this.sprite.on("mousedown", () => this.$emit("mousedown", this.sprite));
		// When the PIXI renderer starts.
		this.EventBus.$on("ready", () => {
			// Set the initial position.
			this.sprite.x = 200;
			this.sprite.y = 1000;
			this.sprite.anchor.set(0.5);

			// Opt-in to interactivity.
            this.sprite.interactive = true;
            
            console.log(this.sprite)

			// Add the sprite to the parent container or the root app stage.
			if (this.$parent.container) {
				this.$parent.container.addChild(this.sprite);
			} else {
				this.PIXIWrapper.PIXIApp.stage.addChild(this.sprite);
			}

			// Emit an event for this sprite on every tick.
			// Great way to kill performance.
			// this.PIXIWrapper.PIXIApp.ticker.add(delta =>
			// 	this.$emit("tick", this.sprite, delta)
			// );
		});
	}
};
</script>