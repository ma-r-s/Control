<script>
	import { browser } from '$app/environment';
	let waves = [
		{ amplitude: 75, frequency: 0.02, phase: 2, color: 'Red' },
		{ amplitude: 50, frequency: 0.04, phase: 1, color: 'Green' },
		{ amplitude: 25, frequency: 0.06, phase: 4, color: 'Blue' }
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
	let w = 0;
	if (browser) {
		w = window.innerWidth;
		window.addEventListener('resize', () => {
			w = window.innerWidth;
		});
	}
	setInterval(() => {
		if (isPlaying) {
			t += 1;
		}
	}, 1000 / 60);
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

	<div class="flex gap-4 md:flex-row flex-col justify-between py-3">
		{#each waves as wave, idx}
			<div>
				<p class="font-bold py-3">{wave.color} wave</p>
				<input
					type="range"
					class="w-full range range-sm range-info"
					min="0"
					max="75"
					step="1"
					bind:value={waves[idx].amplitude}
				/>
				<input
					type="range"
					class="w-full range range-sm range-warning"
					min="0"
					max="0.1"
					step="0.001"
					bind:value={waves[idx].frequency}
				/>
				<input
					type="range"
					class="w-full range range-sm range-error"
					min="0"
					max={Math.PI * 2}
					step="0.01"
					bind:value={waves[idx].phase}
				/>
				<div class="flex justify-between">
					<p>Ampl: {waves[idx].amplitude}</p>
					<p>Freq: {waves[idx].frequency}</p>
					<p>Phase: {waves[idx].phase}</p>
				</div>
			</div>
		{/each}
	</div>
	<p class="text-xl font-semibold py-3">Independent Waves</p>

	<svg class="w-full my-5 border rounded-2xl">
		<text x="40" y="18" class="fill-black dark:fill-white">f(t)</text>
		<text x={w - 80} y="65" class="fill-black dark:fill-white">t</text>
		<line x1="0" y1="50%" x2="100%" y2="50%" class="stroke-black dark:stroke-white" />
		<line x1={yAxis} y1="0" x2={yAxis} y2="100%" class="stroke-black dark:stroke-white" />

		{#each waves as wave}
			<path
				stroke={wave.color}
				fill="none"
				d={Array.from({ length: w }, (_, x) => {
					const y = wave.amplitude * Math.sin(wave.frequency * (x + t - yAxis) + wave.phase);
					return x === 0 ? `M ${x},${75 + y}` : `L ${x},${75 + y}`;
				}).join(' ')}
			/>
		{/each}
	</svg>
	<p class="text-xl font-semibold py-3">Waves Sum</p>
	<svg class="w-full my-5 border rounded-2xl h-72">
		<text x="40" y="18" class="fill-black dark:fill-white">f(t)</text>
		<text x={w - 80} y="130" class="fill-black dark:fill-white">t</text>

		<line x1="0" y1="50%" x2="100%" y2="50%" class="stroke-black dark:stroke-white" />
		<line x1={yAxis} y1="0" x2={yAxis} y2="100%" class="stroke-black dark:stroke-white" />
		o
		<path
			class="stroke-black dark:stroke-white"
			fill="none"
			d={Array.from({ length: w }, (_, x) => {
				const sum = waves.reduce((acc, wave) => {
					return acc + wave.amplitude * Math.sin(wave.frequency * (x + t - yAxis) + wave.phase);
				}, 0);
				return x === 0 ? `M ${x},${144 + sum}` : `L ${x},${144 + sum}`;
			}).join(' ')}
		/>
	</svg>
</main>
