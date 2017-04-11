<template>
	<div><div @scroll="scroll"><div></div></div><div @scroll="scroll"><div></div></div></div>
</template>

<script>

var requestAnimationFrame = window.requestAnimationFrame.bind(window) ||
	window.mozRequestAnimationFrame.bind(window) ||
	window.webkitRequestAnimationFrame.bind(window) ||
	function(fn) {
		
		return window.setTimeout(fn, 20);
	};

module.exports = {

	// thanks to https://github.com/marcj/css-element-queries
	methods: {
		scroll: function(ev) {
			
			if ( this.afci !== 0 )
				return;

			this.afci = requestAnimationFrame(function() {
				
				var size = { width: this.$el.offsetWidth, height: this.$el.offsetHeight };
				if ( size.width != this.lastSize.width || size.height != this.lastSize.height ) {
	
					this.lastSize = size;
					this.$emit('resize', size);
				};

				this.afci = 0;
			}.bind(this));

			this.reset();
		}
	},
	mounted: function() {
		
		this.afci = 0; // https://html.spec.whatwg.org/multipage/webappapis.html#animation-frame-callback-identifier
		
		var style = 'position: absolute; left: 0; top: 0; right: 0; bottom: 0; overflow: hidden; z-index: -1; visibility: hidden;';
		var styleChild = 'position: absolute; left: 0; top: 0; transition: 0s;';

		this.$el.style.cssText = style;
		
		if ( this.$el.offsetParent !== this.$el.parentNode )
			this.$el.parentNode.style.position = 'relative';
		
		var expand = this.$el.childNodes[0];
		expand.style.cssText = style;
		expand.firstChild.style.cssText = styleChild + ' width: 100000px; height: 100000px;';
		
		var shrink = this.$el.childNodes[1];
		shrink.style.cssText = style;
		shrink.firstChild.style.cssText = styleChild + ' width: 200%; height: 200%;';
		
		this.lastSize = { width: this.$el.offsetWidth, height: this.$el.offsetHeight };

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