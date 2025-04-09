<script>
	import pipe from '$lib/images/pipe.png';
	import { pipeSeperationSpace } from '$lib/js/constants.js';

	export let pipeWidth;
	export let topHeight;
	export let bottomHeight;
	export let openingHeight;

</script>

<!-- the height of the opening is determined by the margin-bottom in this style -->
<img
	style="margin-bottom: {openingHeight}px; height: {topHeight}px; width: {pipeWidth}px; transform: rotate(180deg);"
	src={pipe}
	alt="pipe top"
/>

<!-- the height where the opening starts is determined by the height in this style -->
<img 
	style="height: {bottomHeight}px; width: {pipeWidth}px;" 
	src={pipe} 
	alt="pipe bottom" 
/>
