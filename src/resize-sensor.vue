<style>
@keyframes resizeSensorVisibility {
	from { top: 0; }
}
</style>

<script>

var requestAnimationFrame = (
	window.requestAnimationFrame ||
	window.mozRequestAnimationFrame ||
	window.webkitRequestAnimationFrame ||
	function(fn) {
		
		return this.setTimeout(fn, 20);
	}
	).bind(window);

module.exports = {

	// thanks to https://github.com/marcj/css-element-queries
	methods: {
		update: function(ev) {
			
			if ( this.afci !== 0 )
				return;

			this.afci = requestAnimationFrame(function() {
				
				var size = { width: this.$el.offsetWidth, height: this.$el.offsetHeight };
				
				if ( size.width !== this.lastSize.width || size.height !== this.lastSize.height ) {
	
					this.lastSize = size;
					this.$emit('resize', size);
				};

				this.afci = 0;
			}.bind(this));

			this.reset();
		}
	},
	render: function(create) {
		
		var vm = this;
		
		var style = 'position: absolute; left: 0; top: 0; right: 0; bottom: 0; overflow: hidden; z-index: -1; visibility: hidden; animation-name: resizeSensorVisibility;';
		var styleChild = 'position: absolute; left: 0; top: 0;';

		return create('div', {
			style: style,
			on: {
				'~animationstart': vm.update.bind(vm),
			}
		},[
			create('div', {
				style: style,
				on: {
					scroll: vm.update.bind(vm)
				}
			}, [
				create('div', {
					style: styleChild + ' width: 100000px; height: 100000px;'
				})
			]),
			create('div', {
				style: style,
				on: {
					scroll: vm.update.bind(vm)
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
		
		this.afci = 0; // https://html.spec.whatwg.org/multipage/webappapis.html#animation-frame-callback-identifier

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

		this.lastSize = { width: -1, height: -1 };

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