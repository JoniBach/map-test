<script type="ts">
	import { onMount } from 'svelte';
	import location from '$lib/images/placeholder.png';

	let container;
	let map;
	let marker;
	let zoom = 18;
	let center = { lat: 25.12524288324294, lng: 121.47016458217341 };
	onMount(async () => {
		// @ts-ignore
		//init Map
		map = new google.maps.Map(container, {
			zoom,
			center
			// styles: maps_style
		});

		//    Marker
		marker = new google.maps.Marker({
			position: center,
			map: map,
			title: 'Hello World!',
			icon: location
		});
	});
</script>

<svelte:head>
	<title>Map</title>
	<meta name="description" content="Demo Google Map API" />
</svelte:head>

<div class="full-screen" bind:this={container}></div>

<style>
	.full-screen {
		width: 100%;
		height: 90vh;
	}
</style>
