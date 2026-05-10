<script>
	import './layout.css';
	// import favicon from '$lib/assets/favicon.svg';

	let { children } = $props();
</script>

<svelte:head><link rel="icon" href="/75wfavicon.png" /></svelte:head>
{@render children()}
