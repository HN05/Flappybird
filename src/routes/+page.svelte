<script>
	// Importing sounds
	import pointSoundSrc from '$lib/sounds/point.mp3';
	import hitSoundSrc from '$lib/sounds/hit.mp3';
	import wingSoundSrc from '$lib/sounds/wing.mp3';

	import Background from '$lib/background.svelte';
	import GameOver from '$lib/gameOver.svelte';
	import Bird from '$lib/bird.svelte';

	import { onMount } from 'svelte';
	import {
		pipeSeperationSpace,
		extraFirstPipeSpace,
		pipeWidth,
		birdWidth,
		birdHeight,
		openingHeight,
		horizontalVelocity,
		jumpVelocity,
		gravity,
		offset,
		yStart,
		xStart
	} from '$lib/js/constants.js';

	let screenHeight;
	let pointSound;
	let hitSound;
	let wingSound;

	// game variables
	let backgroundPos = 0;
	let y = yStart; // y is top part of bird
	let x = xStart; // x is left part of bird
	let pressedKey = false;
	let finished = false;
	let score = 0;
	let velocity = jumpVelocity; // velocity is pointed downwards
	let pipeHeights = [];
	let previousTimestamp = 0;

	let gameOverVisable = false; // for animation only

	function generatePipeHeight() {
		// create random number beetween 0.2 and 1.8
		let random = Math.max(Math.min(Math.random(), 0.9), 0.1) * 2;

		let topPipeHeight = (screenHeight - openingHeight) / 2;
		topPipeHeight *= random;

		const bottomPipeHeight = screenHeight - openingHeight - topPipeHeight;

		return {
			pipeWidth: pipeWidth,
			openingHeight: openingHeight,
			topHeight: topPipeHeight,
			bottomHeight: bottomPipeHeight
		};
	}

	function initPipeHeights() {
		for (let i = 0; i < 10; i++) {
			pipeHeights[i] = generatePipeHeight();
		}
	}

	function move(deltaTime) {
		// handles jump
		if (pressedKey && y - birdHeight > 0) {
			velocity = jumpVelocity; // increase upward velocity
			const newWingSound = new Audio(wingSoundSrc);
			newWingSound.play();
			pressedKey = false;
		} else if (pressedKey) {
			pressedKey = false;
		}

		// (1000 / 120) = time (in milliseconds) per frame for 120 fps
		const timeFactor = deltaTime / (1000 / 120); // the game was tuned for 120Hz screen

		velocity += gravity * timeFactor;

		// makes sure the bird never goes below screen
		const newY = y + velocity * timeFactor;
		const ground = screenHeight - birdHeight + offset;
		y = Math.min(newY, ground);

		// moves horizontally
		backgroundPos -= horizontalVelocity * timeFactor;
		x += horizontalVelocity * timeFactor;

		// generates new pipes
		if (pipeHeights.length - 3 < score) {
			pipeHeights[pipeHeights.length] = generatePipeHeight();
		}
	}

	function checkIfFinished() {
		// bird hits the ground
		if (!(y - offset < screenHeight - birdHeight)) {
			finished = true;
		}

		const firstPipe = extraFirstPipeSpace + pipeSeperationSpace;
		const passedPipes = score * (pipeWidth + pipeSeperationSpace);
		const pipeStart = firstPipe + passedPipes + offset;
		const pipeEnd = pipeStart + pipeWidth - offset;

		const birdEnd = x + birdWidth;

		// checks if bird is inside pipe x area
		const birdStartInside = x - offset > pipeStart && x + offset < pipeEnd;
		const birdEndInside = birdEnd - offset > pipeStart && birdEnd + offset < pipeEnd;

		if (birdStartInside || birdEndInside) {
			const pipeHeight = pipeHeights[score];

			// checks if bird hits bottom pipe
			if (y + birdHeight > screenHeight - pipeHeight.bottomHeight + offset) {
				finished = true;
			}

			// chekcs if bird hits top pipe
			if (y < pipeHeight.topHeight - offset) {
				finished = true;
			}
		}

		if (x > pipeEnd) {
			score += 1;
			pointSound.play();
		}
	}

	// gameloop runs once per frame
	function gameloop(timestamp) {
		// deltaTime is in milliseconds
		const deltaTime = timestamp - previousTimestamp;
		previousTimestamp = timestamp;

		move(deltaTime);
		checkIfFinished();

		if (!finished) {
			requestAnimationFrame(gameloop);
		} else {
			hitSound.play();
			gameOverVisable = true;
		}
	}

	function initGameloop(timestamp) {
		initPipeHeights();
		previousTimestamp = timestamp;
		gameloop(timestamp);
	}

	onMount(() => {
		screenHeight = window.innerHeight;

		const handleResize = () => {
			screenHeight = window.innerHeight;
		};

		window.addEventListener('resize', handleResize);

		requestAnimationFrame(initGameloop);

		return () => {
			window.removeEventListener('resize', handleResize);
		};
	});

	function keyUpHandler(e) {
		if (e.key === ' ' || e.key === 'ArrowUp' || e.key === 'w') {
			pressedKey = true;
		}
	}

	function reset() {
		y = yStart;
		x = xStart;
		pressedKey = false;
		finished = false;
		score = 0;
		backgroundPos = 0;
		velocity = jumpVelocity;

		requestAnimationFrame(initGameloop);
	}
</script>

<svelte:head>
	<title>Flappy Bird</title>
	<meta name="description" content="The flappy bird game" />
</svelte:head>

<svelte:window on:keyup={keyUpHandler} />

<audio src={pointSoundSrc} bind:this={pointSound} />
<audio src={hitSoundSrc} bind:this={hitSound} />

{#if finished}
	<GameOver {score} onReset={reset} bind:gameOverVisable />
{/if}

<!-- gameview -->
<div>
	{#if !finished}
		<h2>{score}</h2>
	{/if}

	<Bird {y} {velocity} />

	<Background {backgroundPos} {pipeHeights} />
</div>

<style>
	h2 {
		font-size: 70px;
		font-weight: bolder;
		top: 5%;
		width: 100%;
		text-align: center;
		position: absolute;
		z-index: 4;
		color: white;
	}
</style>
