<script lang="ts">
	import { onMount } from 'svelte';
	import { Loader } from '@googlemaps/js-api-loader';
	import { PUBLIC_MAP_KEY } from '$env/static/public';
	let map: any, infoWindow: any, marker: any;

	const loader = new Loader({
		apiKey: PUBLIC_MAP_KEY,
		version: 'weekly',
		libraries: ['places']
	});

	const mapOptions = {
		center: {
			lat: 0,
			lng: 0
		},
		zoom: 4
	};

	function handleLocationError(browserHasGeolocation, infoWindow, pos) {
		infoWindow.setPosition(pos);
		infoWindow.setContent(
			browserHasGeolocation
				? 'Error: The Geolocation service failed.'
				: "Error: Your browser doesn't support geolocation."
		);
		infoWindow.open(map);
	}

	function handleLocation() {
		if (navigator.geolocation) {
			navigator.geolocation.getCurrentPosition(
				(position) => {
					const pos = {
						lat: position.coords.latitude,
						lng: position.coords.longitude
					};

					marker.setPosition(pos);
					marker.setMap(map);
					marker.setTitle('You are here!');

					map.setCenter(pos);
				},
				() => {
					handleLocationError(true, infoWindow, map.getCenter());
				}
			);
		} else {
			// Browser doesn't support Geolocation
			handleLocationError(false, infoWindow, map.getCenter());
		}
	}

	const loadMarker = () => {
		loader.importLibrary('marker').then(({ Marker }) => {
			marker = new Marker();
		});
	};

	const loadMap = () => {
		loader
			.importLibrary('maps')
			.then(({ Map, InfoWindow }) => {
				map = new Map(document.getElementById('map'), mapOptions);
				infoWindow = new InfoWindow();

				map.controls[google.maps.ControlPosition.TOP_CENTER].push(
					document.getElementById('control-button')
				);
			})
			.catch((e) => {
				// do something
				window.alert('Map failed to load');
			});
	};

	const loadRoute = () => {
		loader.importLibrary('routes').then(({ DirectionsService, DirectionsRenderer }) => {
			const directionsService = new DirectionsService();
			const directionsRenderer = new DirectionsRenderer();
			directionsRenderer.setMap(map);
			directionsService.route(
				{
					origin: {
						query: 'Risca'
					},
					destination: {
						query: 'Bristol'
					},
					travelMode: google.maps.TravelMode.DRIVING
				},
				(response, status) => {
					if (status === 'OK') {
						directionsRenderer.setDirections(response);
					} else {
						window.alert('Directions request failed due to ' + status);
					}
				}
			);
		});
	};

	onMount(async () => {
		loadMarker();
		loadMap();
		loadRoute();
	});
</script>

<div class="full-screen" id="map" />
<div id="info-window" />
<div id="marker" />
<button class="custom-map-control-button" id="control-button" on:click={handleLocation}
	>Pan to Current Location</button
>

<style>
	.full-screen {
		width: 100%;
		height: 100vh;
		overflow: hidden;
	}
	.custom-map-control-button {
		background-color: #fff;
		border: 0;
		border-radius: 2px;
		box-shadow: 0 1px 4px -1px rgba(0, 0, 0, 0.3);
		margin: 10px;
		padding: 0 0.5em;
		font:
			400 18px Roboto,
			Arial,
			sans-serif;
		overflow: hidden;
		height: 40px;
		cursor: pointer;
	}
	.custom-map-control-button:hover {
		background: rgb(235, 235, 235);
	}
</style>
