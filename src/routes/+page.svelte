<script>
  import { onMount, setContext } from "svelte";
  import { mapbox, key } from "./store.js";
  import "mapbox-gl/dist/mapbox-gl.css";

  let map;
  let accessToken = import.meta.env.VITE_MAPBOX_API_ACCESS_TOKEN;
  let hoveredPolygonId = null;
  let selectedOption = 'Option 1';

  setContext(key, {
    getMap: () => map,
  });

  // Reactive statement to update pmtiles and source_layer based on selectedOption
  $: pmtiles = selectedOption === 'Option 1'
	? 'https://germanyclimatedata.s3.eu-central-1.amazonaws.com/municipalities.pmtiles'
	: 'https://germanyclimatedata.s3.eu-central-1.amazonaws.com/districts.pmtiles';

  $: source_layer = selectedOption === 'Option 1'
	? 'gemeinden_simplify200'
	: 'landkreise_simplify200';
  $: console.log(source_layer)

  onMount(async () => {
    const map = new mapbox.Map({
      container: "map_base", // container ID
      center: [10.4515, 51.1657], // starting position [lng, lat]
      zoom: 5.7, // starting zoom,
      accessToken:
        "pk.eyJ1Ijoia2FsZGVyYS1hYSIsImEiOiJjbDh0ejgycG8wNnpxM3duYnMwanUxeXJ3In0.P6Gw61fus39Y3C_WYDbkdA",
    });
  });
</script>

<div id="map_base">
  {#if map}
    <slot />
  {/if}
</div>

<div class="overlay-box">
	<label>
		<input
			type="radio"
			bind:group={selectedOption}
			value="Option 1"
		/>
		Option 1
	</label>
	<br />
	<label>
		<input
			type="radio"
			bind:group={selectedOption}
			value="Option 2"
		/>
		Option 2
	</label>
</div>

<style>
  #map_base {
    position: absolute;
    top: 0;
    bottom: 0;
    width: 100%;
  }
  .overlay-box {
		position: fixed;
		top: 10px;
		left: 10px;
		background-color: rgba(255, 255, 255, 0.9);
		border: 1px solid #ccc;
		border-radius: 4px;
		padding: 1rem;
		z-index: 1000; /* Ensures it overlays other elements */
  }
</style>
