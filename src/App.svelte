<script>
	import { fly } from 'svelte/transition';
	import Keypad from './lib/Keypad.svelte';

	let numberInput = '';
	let total = 0;

	function addToEquation(value) {
		numberInput += value;
	}
	function removeFromEquation() {
		numberInput = numberInput.slice(0,numberInput.length-1);
	}

	const clear = () => {
		total = 0;
		numberInput = '';
	};

	function calculate() {
		if (numberInput !== '') {
			numberInput = result().toString();
		}
	}

	function replaceAll(string, search, replace) {
		return string.split(search).join(replace);
	}

	function formatString(value) {
		return replaceAll(replaceAll(value, '*', 'x'), '/', '÷');
	}

	// computed
	let result = () => {
		if (!isNaN(Number(numberInput.slice(-1)))) {
			return eval(numberInput);
		}
		return eval(numberInput.slice(0, -1));
	};

	// reactive values
	$: if (numberInput !== '' && !isNaN(Number(numberInput.slice(-1))) && numberInput != result()) {
		total = result().toString();
	}
</script>

<section class="app">
	<div class="results">
		<div class="calculations">{numberInput}</div>
		{#if total !== 0}
			<input transition:fly={{ x: 10, duration: 800 }} type="text" class="total" value={total} />
		{/if}
	</div>

	<div class="input-pad">
		<Keypad type="clear" on:click={clear}>AC</Keypad>
		<Keypad type="operator" on:click={() => addToEquation('()')}>()</Keypad>
		<!-- TODO: logic to add ( or ) based on situation -->
		<Keypad type="operator" on:click={() => addToEquation('%')}>%</Keypad>
		<Keypad type="operator" on:click={() => addToEquation('/')}>÷</Keypad>

		<Keypad on:click={() => addToEquation(7)}>7</Keypad>
		<Keypad on:click={() => addToEquation(8)}>8</Keypad>
		<Keypad on:click={() => addToEquation(9)}>9</Keypad>
		<Keypad type="operator" on:click={() => addToEquation('*')}>x</Keypad>

		<Keypad on:click={() => addToEquation(4)}>4</Keypad>
		<Keypad on:click={() => addToEquation(5)}>5</Keypad>
		<Keypad on:click={() => addToEquation(6)}>6</Keypad>
		<Keypad type="operator" on:click={() => addToEquation('-')}>-</Keypad>

		<Keypad on:click={() => addToEquation(1)}>1</Keypad>
		<Keypad on:click={() => addToEquation(2)}>2</Keypad>
		<Keypad on:click={() => addToEquation(3)}>3</Keypad>
		<Keypad type="operator" on:click={() => addToEquation('+')}>+</Keypad>

		<Keypad type="clear" on:click={() => removeFromEquation()}>C</Keypad>
		<Keypad on:click={() => addToEquation(0)}>0</Keypad>
		<Keypad on:click={() => addToEquation('.')}>.</Keypad>
		<Keypad type="equal" on:click={() => calculate()}>=</Keypad>
	</div>
</section>

<style>
	:global(body) {
		color: #333;
		margin: 0;
		padding: 20px;
		box-sizing: border-box;
		font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen-Sans, Ubuntu, Cantarell, 'Helvetica Neue', sans-serif;
	}

	.app {
		max-width: 300px;
	}

	.input-pad {
		display: grid;
		grid-template-columns: repeat(4, 25%);
		grid-template-rows: repeat(5, 20%);
		grid-column-gap: 0;
		grid-row-gap: 0;
	}

	.results {
		display: flex;
		height: 94px;
		flex-direction: column;
		text-align: right;
	}

	.calculations {
		height: 20px;
		color: #828282;
		padding: 0 10px;
	}

	.total {
		color: #333;
		text-align: right;
		padding: 10px;
		font-size: 44px;
		margin: 0;
		border: none;
	}
</style>
