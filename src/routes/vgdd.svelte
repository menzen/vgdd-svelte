<script context="module">
	import { browser, dev } from '$app/env';

	// we don't need any JS on this page, though we'll load
	// it in dev so that we get hot module replacement...
	export const hydrate = dev;

	// ...but if the client-side router is already loaded
	// (i.e. we came here from elsewhere in the app), use it
	export const router = browser;

	// since there's no dynamic data here, we can prerender
	// it so that it gets served as a static asset in prod
	export const prerender = true;
</script>

<script lang="ts">
	import VGDD from '$lib/VGDD.svelte';
</script>

<svelte:head>
	<title>VGDD</title>
</svelte:head>

<div class="content">
	<VGDD />
</div>

<style>
	.content {
		width: 100%;
		max-width: var(--column-width);
		margin: 0 auto 0 auto;
	}
</style>
