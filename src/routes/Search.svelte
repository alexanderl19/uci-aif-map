<script lang="ts">
	import { Document } from 'flexsearch';
	import { booths } from './booths';

	const alphabetical = booths.sort(({ name: nameA }, { name: nameB }) =>
		nameA.localeCompare(nameB)
	);

	const index = new Document({
		document: {
			id: 'booth',
			index: ['name', 'booth']
		},
		tokenize: 'full'
	});
	alphabetical.forEach((booth, i) => {
		index.add(booth);
	});
	let search = '';
	$: resultIds = index.search(search).map(({ result }) => result[0]);
	$: results = alphabetical.filter(({ booth }) => resultIds.includes(booth));
</script>

<div class="search">
	<input bind:value={search} />
	{#if search}
		<ol>
			{#each results as { name, booth }}
				<li>
					<span>{name}</span>
					<span class="booth-number">{booth}</span>
				</li>
			{/each}
		</ol>
	{:else}
		<ul>
			{#each alphabetical as { name, booth }}
				<li>
					<span>{name}</span>
					<span class="booth-number">{booth}</span>
				</li>
			{/each}
		</ul>
	{/if}
</div>

<style lang="scss">
	.search {
		position: fixed;
		width: 384px;
		top: 8px;
		left: 8px;
		bottom: 8px;
		box-sizing: border-box;
		padding: 16px;
		background-color: rgba(255, 255, 255, 0.455);
		backdrop-filter: blur(10px);
		border-radius: 8px;
		z-index: 1000;
		overflow-y: auto;
	}

	ol,
	ul {
		margin: 0;
		padding: 0;

		li {
			display: grid;
			grid-template-columns: 1fr 8ch;
			gap: 4px;
			margin-bottom: 8px;

			.booth-number {
				font-variant-numeric: lining-nums tabular-nums;
			}
		}
	}
</style>
