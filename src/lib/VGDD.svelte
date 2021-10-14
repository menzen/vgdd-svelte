<script lang="ts">
	import { onMount } from 'svelte';

	interface Item {
		active?: boolean;
		id: string;
		number: number | string;
		label: string;
	}

	const numbers = [7, 8, 9, 4, 5, 6, 1, 2, 3, 'DEL', 0, 'OK'];

	const onClickNumber = (event, key) => {
		if (event) {
			animate(event.target);
		}

		if (key === 'DEL') {
			if (isAnyActive) {
				items = items.filter((item) => !item.active);
				isAnyActive = false;
				localStorage.setItem('items', JSON.stringify(items));
				return;
			}

			number = number.slice(0, -1);
			return;
		}

		if (key === 'OK') {
			if (label) {
				items = [
					...items,
					{
						id: number + ' - ' + label,
						number,
						label: label
					}
				].sort((a, b) => a.label.localeCompare(b.label));
				number = '';
				label = '';
				refNumber.focus();
			} else {
				refLabel.focus();
			}

			localStorage.setItem('items', JSON.stringify(items));
			return;
		}

		number += key;
	};

	const onSubmit = () => onClickNumber(null, 'OK');

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

	const onKeyDownButton = (event) => {
		event.stopPropagation();
		event.preventDefault();

		if (event.code === 'Enter') {
			refLabel.focus();
		}
	};

	const onKeyDownNumber = (event) => {
		if (event.code === 'Enter') {
			onClickNumber(null, 'OK');
		}
	};

	const onKeyDownLabel = (event) => {
		if (event.code === 'Enter') {
			onClickNumber(null, 'OK');
		}
	};

	const animate = (el) => {
		el.classList.add('animate');
		setTimeout(() => el.classList.remove('animate'), 150);
	};

	let items: Item[] = [];
	let label = '';
	let number = '';
	let refNumber: HTMLElement;
	let refLabel: HTMLElement;
	let isAnyActive = false;

	onMount(() => {
		const store = window.localStorage.getItem('items');

		if (store) {
			items = JSON.parse(store);
		}

		refNumber.focus();
	});
</script>

<div>
	<form on:submit|preventDefault={onSubmit}>
		<div class="display">
			<input
				bind:value={number}
				bind:this={refNumber}
				on:keydown={(event) => onKeyDownNumber(event)}
				inputmode="none"
			/>
			<input
				bind:value={label}
				bind:this={refLabel}
				on:keydown={(event) => onKeyDownLabel(event)}
			/>
		</div>
		<div class="buttons">
			{#each numbers as number}
				<button
					type="button"
					on:keydown={(event) => onKeyDownButton(event)}
					on:click={(event) => onClickNumber(event, number)}>{number}</button
				>
			{/each}
		</div>
	</form>

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
	.display {
		text-align: center;
		padding: 20px;
		background: #000;
		border: 2px solid #ddd;
		border-radius: 5px;
	}

	:global(.animate) {
		transform: scale(1.1);
		background: orange !important;
	}

	.display input {
		display: block;
		width: 100%;
		font-size: 30px;
		color: #fff;
	}

	input {
		text-align: center;
		border: none;
		background: transparent;
		outline: none;
	}

	.buttons {
		display: flex;
		flex-wrap: wrap;
		justify-content: space-around;
	}

	.buttons button {
		width: 33%;
		height: 80px;
		font-size: 30px;
		background: #000;
		color: #fff;
		border-radius: 5px;
		border: 1px solid #ddd;
	}

	ul {
		list-style-type: none;
		padding: 0;
	}

	li {
		padding: 15px 10px;
		border-top: 1px solid #ddd;
		border-bottom: 1px solid #ddd;
		margin-top: -1px;
		font-size: 24px;
	}

	.active {
		background: red;
		opacity: 0.5;
	}
</style>
