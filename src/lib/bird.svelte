<script>
  import bird from '$lib/images/flappyBird.png';
	import birdDown from '$lib/images/flappyBirdDown.png';

  import { xStart, birdWidth, birdHeight} from '$lib/js/constants.js';

  export let y
  export let velocity

</script>

<img style="top:{y}px; left: {xStart}px; width: {birdWidth}px; height: {birdHeight}px;" src={velocity < 0 ? birdDown : bird} alt="bird" class="bird" />

<style>
  .bird {
		position: absolute;
		z-index: 3;
	}
</style>