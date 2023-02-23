<script lang="ts">
	let password = '';
	let length = 32;
	let message = 'copy to clipboard';

	const rules = [
		{
			name: 'Lowercase',
			enabled: true,
			chars: ['abcdefghijklmnopqrstuvwxyz']
		},
		{
			name: 'Uppercase',
			enabled: true,
			chars: ['ABCDEFGHIJKLMNOPQRSTUVWXYZ']
		},
		{
			name: 'Numbers',
			enabled: true,
			chars: ['0123456789']
		},
		{
			name: 'Special',
			enabled: true,
			chars: ['+*%&?!#$']
		}
	];

	const copyToClipboard = async () => {
		await navigator.clipboard.writeText(password);
		message = 'copied!';
	};
	const generate = () => {
		const buffer = new Uint8Array(length);
		crypto.getRandomValues(buffer);
		const chars = rules
			.filter((rule) => rule.enabled)
			.map((rule) => rule.chars)
			.flat()
			.join('');

		password = Array.from(buffer)
			.map((val) => chars[val % chars.length])
			.join('');
		message = 'copy to clipboard';
	};
	$: {
		length;
		rules;
		generate();
	}
</script>

<main class="container">
	<hgroup>
		<h1>Generate a Password</h1>
		<details>
			<summary>How it Works</summary>
			<p>
				It uses the Web Crypto API in your Browser to generate random numbers with
				Crypto.getRandomValues() and than mappes them by the selected character sets.
			</p>
		</details>
	</hgroup>
	<div>
		<label>
			Generated Password
			<input readonly type="text" value={password} />
		</label>
		<button on:click={copyToClipboard}>{message}</button>
		<button on:click={generate}>generate</button>
	</div>

	<div>
		<label>
			Length ({length})
			<input min="1" max="64" type="range" bind:value={length} />
		</label>
	</div>
	<div class="grid">
		{#each rules as rule}
			<div>
				<input type="checkbox" bind:checked={rule.enabled} />

				<label
					>{rule.name}
					<input type="text" bind:value={rule.chars} />
				</label>
			</div>
		{/each}
	</div>
</main>
