<script>
	import P5 from 'p5-svelte';
	import FourierTransform from 'fourier-transform';
	let waves = [
		{ amplitude: 100, frequency: 0.02, phase: 0, color: [255, 0, 0] },
		{ amplitude: 80, frequency: 0.04, phase: 0, color: [0, 255, 0] },
		{ amplitude: 60, frequency: 0.06, phase: 0, color: [0, 0, 255] }
	];
	let yAxis = 30;
	let isPlaying = false; // Animation play state
	let t = 0;
	const startAnimation = () => {
		isPlaying = true;
	};

	const stopAnimation = () => {
		isPlaying = false;
		t = 0;
	};
	const sketch = (p) => {
		p.setup = () => {
			p.createCanvas(p.windowWidth, 300);
			p.frameRate(60);
		};

		p.draw = () => {
			p.background(0);

			// Draw axis
			p.stroke(255);
			p.line(0, p.height / 2, p.width, p.height / 2);
			p.line(yAxis, 0, yAxis, p.height);

			waves.forEach((wave) => {
				p.noFill();
				p.stroke(wave.color);
				p.beginShape();
				for (let x = 0; x < p.width; x++) {
					let y = wave.amplitude * p.sin(wave.frequency * (x + t - yAxis) + wave.phase);
					p.vertex(x, p.height / 2 - y);
				}
				p.endShape();
			});
			if (isPlaying) t += 0.5;
		};

		p.windowResized = () => {
			p.resizeCanvas(p.windowWidth, 300);
		};
	};

	const sumSketch = (p) => {
		p.setup = () => {
			p.createCanvas(p.windowWidth, 300);
			p.frameRate(60);
		};

		p.draw = () => {
			p.background(0);

			p.stroke(255);
			p.line(0, p.height / 2, p.width, p.height / 2);
			p.line(yAxis, 0, yAxis, p.height);

			p.noFill();
			p.stroke(255);
			p.beginShape();
			for (let x = 0; x < p.width; x++) {
				let sum = 0;
				waves.forEach((wave) => {
					sum += wave.amplitude * p.sin(wave.frequency * (x + t - yAxis) + wave.phase);
				});
				let y = sum;
				p.vertex(x, p.height / 2 - y);
			}
			p.endShape();
			if (isPlaying) {
				t += 0.5;
			}
		};
	};
</script>

<main class="m-6">
	<div class="flex justify-between">
		<h1 class="text-2xl font-bold">Sine Wave Visualization</h1>

		{#if isPlaying}
			<button class="btn" on:click={stopAnimation}>Stop</button>
		{:else}
			<button class="btn" on:click={startAnimation}>Play</button>
		{/if}
	</div>

	<div class="flex gap-4">
		{#each waves as _, idx}
			<div class="w-1/3">
				<p>Wave {idx + 1}</p>
				<input
					type="range"
					class="w-full range range-sm range-primary"
					min="0"
					max="150"
					step="1"
					bind:value={waves[idx].amplitude}
				/>
				<input
					type="range"
					class="w-full range range-sm range-secondary"
					min="0"
					max="0.1"
					step="0.001"
					bind:value={waves[idx].frequency}
				/>
				<input
					type="range"
					class="w-full range range-sm range-accent"
					min="0"
					max={Math.PI * 2}
					step="0.01"
					bind:value={waves[idx].phase}
				/>
				<p>{(waves[idx].phase / Math.PI).toFixed(2)} pi</p>
			</div>
		{/each}
	</div>
	<div class="flex w-full my-5 justify-center">
		<div class="border rounded-md overflow-hidden w-fit">
			<P5 {sketch} />
		</div>
	</div>
	<div class="flex w-full my-5 justify-center">
		<div class="border rounded-md overflow-hidden w-fit">
			<P5 sketch={sumSketch} />
		</div>
	</div>
</main>
