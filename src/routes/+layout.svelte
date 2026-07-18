<script lang="ts">
	import '@fontsource-variable/kumbh-sans/wght.css';
	import '@fontsource-variable/roboto-slab/wght.css';
	import '@fontsource/space-mono/400.css';
	import '@fontsource/space-mono/700.css';
	import './layout.css';
	import favicon from '$lib/assets/favicon-32x32.png';

	let { children } = $props();
</script>

<svelte:head>
	<meta name="description" content="A simple Pomodoro timer app built with SvelteKit and TailwindCSS." />
	<link rel="icon" href={favicon} />
	<title>Pomodoro App</title>
</svelte:head>
{@render children()}
