<script lang="ts">
	import 'ol/ol.css';

	import { onDestroy, onMount } from 'svelte';
	import Map from 'ol/Map';
	import View from 'ol/View';
	import VectorTile from 'ol/layer/VectorTile';
	import ImageLayer from 'ol/layer/Image';
	import ImageStatic from 'ol/source/ImageStatic';
	import { boundingExtent } from 'ol/extent';
	import { fromLonLat } from 'ol/proj';
	import { stylefunction } from 'ol-mapbox-style';
	import { PMTilesVectorSource } from 'ol-pmtiles';
	import { light } from './protomaps-light';

	import aifMap from './aif-map.png';

	let map: Map | undefined;
	let mapElement: HTMLDivElement;

	onMount(async () => {
		const baseLayer = new VectorTile({
			declutter: true,
			source: new PMTilesVectorSource({
				url: './6fc0106d-74c7-4cf7-9f78-06d473f7cc50.pmtiles',
				attributions: ['© OpenStreetMap']
			}),
			style: null
		});

		stylefunction(baseLayer, light, 'protomaps');

		map = new Map({
			target: mapElement,
			layers: [
				baseLayer,
				new ImageLayer({
					source: new ImageStatic({
						url: aifMap,
						imageExtent: boundingExtent([
							fromLonLat([-117.84426, 33.6472]),
							fromLonLat([-117.84117, 33.64466])
						])
					})
				})
			],
			view: new View({
				center: fromLonLat([(-117.84426 + -117.84117) / 2, (33.6472 + 33.64466) / 2]),
				zoom: 18,
				rotation: 2.5
			})
		});
	});

	onDestroy(() => {
		map = undefined;
	});
</script>

<main>
	<div bind:this={mapElement} class="map" />
</main>

<style lang="scss">
	:global(body) {
		margin: 0;
	}

	main {
		width: 100vw;
		height: 100vh;
	}

	.map {
		height: 100vh;
	}
</style>
