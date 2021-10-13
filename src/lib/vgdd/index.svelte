<script lang="ts">
	import { onMount } from 'svelte';

	interface Item {
		active?: boolean;
		id: string;
		number: number | string;
		label: string;
	}

	const numbers = [9, 8, 7, 6, 5, 4, 3, 2, 1, 'DEL', 0, 'OK'];

	const onClickNumber = (number) => {
		if (number === 'DEL') {
			if (isAnyActive) {
				items = items.filter((item) => !item.active);
				isAnyActive = false;
				return;
			}

			current = current.slice(0, -1);
			return;
		}

		if (number === 'OK') {
			if (value) {
				items = [
					...items,
					{
						id: current + ' - ' + value,
						number: current,
						label: value
					}
				];
				current = '';
				value = '';
			} else {
				ref.focus();
			}

			localStorage.setItem('items', JSON.stringify(items));
			return;
		}

		current += number;
	};

	const onSubmit = () => onClickNumber('OK');

	const onClickItem = (id) => {
		items = items.map((item) => {
			if (item.id === id) {
				item.active = !item.active;
			}

			if (item.active) {
				isAnyActive = true;
			}
			return item;
		});
	};

	let current = '';
	let items: Item[] = [];
	let value = '';
	let ref: HTMLElement;
	let isAnyActive = false;

	$: display = current;

	onMount(() => {
		const store = window.localStorage.getItem('items');
		if (store) {
			items = JSON.parse(store);
		}
	});
</script>

<div>
	<form on:submit|preventDefault={onSubmit}>
		{#each numbers as number}
			<button type="button" on:click={() => onClickNumber(number)}>{number}</button>
		{/each}
		<input bind:value bind:this={ref} />
	</form>

	{#if display}<h1>{display}</h1>{/if}

	{#if items.length}
		<ul>
			{#each items as item}
				<li class:active={item.active} on:click={() => onClickItem(item.id)}>
					{item.number} - {item.label}
				</li>
			{/each}
		</ul>
	{/if}
</div>

<style>
	.active {
		background: lime;
	}
</style>
