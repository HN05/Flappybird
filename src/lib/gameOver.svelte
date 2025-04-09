<script>
	import video from '$lib/videos/tipsAndTricks.mp4';

	export let score;
	export let onReset;
	export let gameOverVisable;

	let highScore = 0;
	let newHighScore = false;

	const storedHighScore = localStorage.getItem('highScore');
	if (storedHighScore) {
		highScore = parseInt(storedHighScore);
	}

	if (score > highScore) {
		highScore = score;
		localStorage.setItem('highScore', highScore);
		newHighScore = true;
	}

	function reset() {
		onReset();
	}

	function keyHandler(e) {
		if (e.key === 'Enter' || e.key === 'm' || e.key === 'r') {
			reset();
		}
	}
</script>

<svelte:window on:keydown={keyHandler} />

<div class="overlay">
	<div class={gameOverVisable ? 'game-over-screen-visable' : 'game-over-screen'}>
		{#if !newHighScore}
			<h1>Game Over</h1>
		{:else}
			<h1>New High Score!</h1>
		{/if}
		<p>Your score: <strong>{score}</strong> <br />High score: <strong>{highScore}</strong></p>
		<button on:click={reset}>Play Again</button>

		<h2>Tips and Tricks</h2>

		<!-- i dont have captions but vscode keeps yelling at me so ignore the error-->
		<!-- svelte-ignore a11y-media-has-caption -->
		<video width="512" height="288" controls>
			<source src={video} type="video/mp4" />
		</video>
	</div>
</div>

<style>
	.game-over-screen,
	.game-over-screen-visable {
		display: flex;
		flex-direction: column;
		align-items: center;
		width: 100%;
		height: 100%;
		background-color: #fff;
		border-radius: 10px;
		box-shadow: 0px 10px 10px rgba(0, 0, 0, 0.1);
	}

	.game-over-screen-visable { animation: fly-in 0.5s;}

	@keyframes fly-in {
		from { transform: translateY(100%); }
		to { transform: translateY(0); }
	}

	h1 {
		font-size: 2rem;
		margin-bottom: 1rem;
		margin-top: 75px;
	}

	h2 {
		margin-top: 50px;
		margin-bottom: 5px;
	}

	p {
		font-size: 1.5rem;
		margin-bottom: 15px;
		line-height: 1.6;
	}

	button {
		font-size: 1.25rem;
		padding: 0.5rem 1rem;
		cursor: pointer;
		background-color: #4caf50;
		border: none;
		color: #fff;
		text-transform: uppercase;
		font-weight: bold;
		border-radius: 4px;
		box-shadow: 0px 2px 4px rgba(0, 0, 0, 0.1);
		transition: all 0.2s ease;
	}

	button:hover {
		background-color: #66bb6a;
		box-shadow: 0px 4px 6px rgba(0, 0, 0, 0.3);
	}

	.overlay {
		position: absolute;
		top: 50%;
		left: 50%;
		width: 600px;
		height: 700px;
		z-index: 5;
		transform: translate(-50%, -50%);
	}

	video {
		border: none;
		border-radius: 5px;
	}
</style>
