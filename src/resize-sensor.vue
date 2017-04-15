<style>
@keyframes resizeSensorVisibility {
	from { top: 0; }
}
</style>

<script>
module.exports = {

	// thanks to https://github.com/marcj/css-element-queries
	data: function() {
		return {
			size: {
				width: -1,
				height: -1
			}
		}
	},
	methods: {
		update: function() {
			
			this.size.width = this.$el.offsetWidth;
			this.size.height = this.$el.offsetHeight;
			this.reset();
		}
	},
	watch: {
		size: {
			deep: true,
			handler: function(size) {
				
				this.$emit('resize', { width: this.size.width, height: this.size.height });
			}
		}
	},
	render: function(create) {
		
		var style = 'position: absolute; left: 0; top: 0; right: 0; bottom: 0; overflow: hidden; z-index: -1; visibility: hidden;';
		var styleChild = 'position: absolute; left: 0; top: 0;';

		return create('div', {
			style: style + ' animation-name: resizeSensorVisibility;',
			on: {
				'~animationstart': this.update,
			}
		},[
			create('div', {
				style: style,
				on: {
					scroll: this.update
				}
			}, [
				create('div', {
					style: styleChild + ' width: 100000px; height: 100000px;'
				})
			]),
			create('div', {
				style: style,
				on: {
					scroll: this.update
				}
			}, [
				create('div', {
					style: styleChild + ' width: 200%; height: 200%;'
				})
			]),
		]);
	},
	beforeDestroy: function() {
		
		this.$emit('resizeSensorBeforeDestroy');
	},
	mounted: function() {
		
		if ( 'attachEvent' in this.$el && !('AnimationEvent' in window) ) {

			var onresizeHandler = function() {

				this.update();
				removeOnresizeEvent();
			}.bind(this);
		
			var removeOnresizeEvent = function() {
				
				this.$el.detachEvent('onresize', onresizeHandler);
				this.$off('resizeSensorBeforeDestroy', removeOnresizeEvent);
			}.bind(this);
			
			this.$el.attachEvent('onresize', onresizeHandler);
			this.$on('resizeSensorBeforeDestroy', removeOnresizeEvent);
		}

		if ( this.$el.offsetParent !== this.$el.parentNode )
			this.$el.parentNode.style.position = 'relative';

		var expand = this.$el.childNodes[0];
		var shrink = this.$el.childNodes[1];

		this.reset = function() {

			expand.scrollLeft = 100000;
			expand.scrollTop = 100000;
			shrink.scrollLeft = 100000;
			shrink.scrollTop = 100000;
		}

		this.reset();
	}
}

</script>
