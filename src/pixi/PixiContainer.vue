<script>
export default {
	// Inject the EventBus and PIXIWrapper objects from the ancestor renderer component.
	inject: ["EventBus", "PIXIWrapper"],
	// Take properties for the x and y position. (Basic, no validation)
	props: ["x", "y"],

	data() {
		return {
			// Keep a reference to the container so children can be added to it.
			container: null
		};
	},

	// At the current time, Vue does not allow empty components to be created without a DOM element if they have children.
	// To work around this, we create a tiny render function that renders to <template><!-- children --></template>.
	render: function(h) {
		return h("template", this.$slots.default);
	},

	created() {
		// Create a new PIXI container and set some default values on it.
		this.container = new this.PIXIWrapper.PIXI.Container();

		// Allow the container to be interacted with.
		this.container.interactive = true;

		// Forward PIXI's pointerdown event through Vue.
		this.container.on("pointerdown", () =>
			this.$emit("pointerdown", this.container)
		);

		// Once the PIXI app in the renderer component is ready, add this container to its parent.
		this.EventBus.$on("ready", () => {
			// You should probably use computed properties to set the position instead.
			this.container.x = this.PIXIWrapper.PIXIApp.screen.width / 2;
			this.container.y = this.PIXIWrapper.PIXIApp.screen.height / 2;

			if (this.$parent.container) {
				// If the parent is another container, add to it.
				this.$parent.container.addChild(this.container);
			} else {
				// Otherwise it's a direct descendant of the renderer stage.
				this.PIXIWrapper.PIXIApp.stage.addChild(this.container);
			}
		});
	}
};
</script>