<script lang="ts">
	import Package from 'sveltesociety.dev-service/src/components/package.svelte';
	import Recipe from 'sveltesociety.dev-service/src/components/recipe.svelte';
	import Video from 'sveltesociety.dev-service/src/components/video.svelte';

	import type { PageData } from './$types';
	export let data: PageData;
</script>

Home page!

<hr />

<main>
	{#each data.items as item}
		{#if item.type === 'package'}
			<Package
				type="Library"
				description={item.description}
				name={item.name}
				author={item.author}
				lastUpdate={item.lastUpdate}
				likes={item.likes}
				tags={item.keywords}
				cover={item.cover}
				connected={item.connected}
				views={item.views}
				liked={item.liked}
				saved={item.saved}
			/>
		{:else if item.type === 'video'}
			<Video
				description={item.description}
				name={item.name}
				author={item.author}
				lastUpdate={item.lastUpdate}
				likes={item.likes}
				tags={item.keywords}
				embed={item.embed}
				connected={item.connected}
				views={item.views}
				liked={item.liked}
				saved={item.saved}
			/>
		{:else if item.type === 'recipe'}
			<Recipe
				description={item.description}
				name={item.name}
				author={item.author}
				lastUpdate={item.lastUpdate}
				likes={item.likes}
				tags={item.keywords}
				preview={item.preview}
				connected={item.connected}
				views={item.views}
				liked={item.liked}
				saved={item.saved}
			/>
		{/if}
	{/each}
</main>

<style>
	main {
		display: flex;
		flex-direction: column;
		padding: 2rem;
		align-content: center;
		align-items: center;
		gap: 1rem;
	}
</style>
