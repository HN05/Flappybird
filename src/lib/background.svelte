<script>
	import Pipe from '$lib/pipe.svelte';
	import background from '$lib/images/background.png';

	import { extraFirstPipeSpace, pipeSeperationSpace } from '$lib/js/constants.js';

	export let backgroundPos;
	export let pipeHeights;
</script>

<div style="margin-left: {backgroundPos}px;">
	<div class="background">
		{#each pipeHeights.slice(0, Math.ceil(pipeHeights.length / 1.5)) as _}
			<img src={background} alt="background" />
		{/each}
	</div>

	<div class="pipe-container" style="margin-left: {extraFirstPipeSpace}px;">
		{#each pipeHeights as pipeHeights}
			<div style="margin-left: {pipeSeperationSpace}px;">
				<Pipe {...pipeHeights} />
			</div>
		{/each}
	</div>
</div>

<style>
	.background {
		display: flex;
		flex-direction: row;
		height: 100%;
		z-index: 1;
		position: absolute;
		overflow: hidden;
	}

	.pipe-container {
		position: absolute;
		z-index: 2;
		display: flex;
		flex-direction: row;
		height: 100%;
		overflow: hidden;
	}
</style>
