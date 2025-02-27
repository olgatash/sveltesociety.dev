<script lang="ts">
	import ComponentCard from '$lib/components/ComponentIndex/Card.svelte';
	import Seo from '$lib/components/Seo.svelte';
	import Select from '$lib/components/Select.svelte';
	import { packageManager } from '$stores/packageManager';
	import CategoryFilters from '$lib/CategoryFilters.svelte';
	import { filterArray, sortArray } from '$utils/arrayUtils';
	import { tick } from 'svelte';

	// eslint-disable-next-line @typescript-eslint/no-explicit-any
	export let data: any[];
	export let categories: string[];
	export let selectedCategories: string[];
	export let sortableFields: { value: string; label: string; asc: boolean }[];
	export let displayTitle = '';
	export let displayTitleSingular = '';
	export let submittingType = '';

	let searchValue: string;
	let sort = sortableFields[0];

	packageManager.subscribe((newValues) => {
		const grouped = newValues.reduce(
			(carry, item) => {
				if (['npm', 'pnpm', 'yarn', 'deno'].includes(item)) {
					carry['npm'].push(item);
				}
				if (['gem', 'bundler'].includes(item)) {
					carry['gem'].push(item);
				}
				return carry;
			},
			{ npm: [], gem: [] } as { npm: Array<string>; gem: Array<string> }
		);
		const corrected = [
			grouped['npm'][grouped['npm'].length - 1] ?? 'npm',
			grouped['gem'][grouped['gem'].length - 1] ?? 'gem'
		];
		if (newValues.join() !== corrected.join()) {
			tick().then(() => packageManager.set(corrected));
		}
	});

	$: filteredData = filterArray(data, searchValue);
	$: sortedData = sortArray(filteredData, sort);
</script>

<Seo title={displayTitle} />

<h1>{displayTitle}</h1>

<CategoryFilters {categories} {selectedCategories} />
<br />
<section class="controls relative grid items-center justify-stretch gap-4">
	<input
		class="searchbar"
		type="text"
		placeholder="Search for {displayTitle.toLowerCase()}..."
		bind:value={searchValue}
	/>
	<div class="grid gap-2 lg:grid-cols-2">
		<Select
			items={sortableFields}
			bind:value={sort}
			label="Sorting"
			showIndicator
			isClearable={false}
		/>
		<Select
			label="Package manager"
			isClearable={false}
			isSearchable={false}
			showIndicator
			handleClear={() => ({})}
			value={$packageManager.map((value) => ({ value }))}
			groupBy={(item) => item.group}
			isMulti={true}
			on:select={({ detail }) => {
				$packageManager = (detail ?? []).map((i) => i.value);
			}}
			items={[
				{ label: 'NPM', value: 'npm', group: 'JS+TS' },
				{ label: 'PNPM', value: 'pnpm', group: 'JS+TS' },
				{ label: 'Yarn', value: 'yarn', group: 'JS+TS' },
				{ label: 'Deno', value: 'deno', group: 'JS+TS' },
				{ label: 'RubyGem', value: 'gem', group: 'Gem' },
				{ label: 'Bundler', value: 'bundler', group: 'Gem' }
			]}
		/>
		<a href="/help/submitting?type={submittingType}" class="submit"
			>Submit a {displayTitleSingular}</a
		>
	</div>
	<span class="searchbar-count"
		>{sortedData.length} result{#if sortedData.length !== 1}s{/if}</span
	>
</section>
<hr />
<section class="mx-auto mb-12 grid grid-cols-1 gap-6 md:grid-cols-2 xl:grid-cols-3">
	{#each sortedData as entry (entry.title)}
		<ComponentCard
			title={entry.title}
			description={entry.description}
			repository={entry.repository}
			stars={entry.stars}
			date={entry.date}
			npm={entry.npm}
			gem={entry.gem}
			jsr={entry.jsr}
			version={entry.version}
		/>
	{/each}
</section>

<style>
	.controls {
		font-family: Overpass;
	}
	.searchbar {
		padding: 20.5px var(--s-2);
		border: 2px solid var(--dark-gray);
		border-radius: 2px;
		grid-row: 1/2;
		font-family: Overpass;
		background: #f3f6f9 url(/images/search-icon.svg) 98% no-repeat;
		margin: 0;
	}
	.searchbar:focus {
		outline: none;
		border: 1px solid var(--secondary);
	}
	.searchbar-count {
		position: absolute;
		top: calc(100% + 1rem);
		right: 0;
	}
</style>
