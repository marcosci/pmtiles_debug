<script>
  import { onMount, setContext } from "svelte";
  import { mapbox, key } from "./store.js";
  import "mapbox-gl/dist/mapbox-gl.css";
  import * as mapboxPmTiles from "mapbox-pmtiles";

  let map;
  let accessToken = import.meta.env.VITE_MAPBOX_API_ACCESS_TOKEN;
    let hoveredPolygonId = null;
  setContext(key, {
    getMap: () => map,
  });

	const municipalities_pmtiles =
		'https://germanyclimatedata.s3.eu-central-1.amazonaws.com/municipalities.pmtiles';

  onMount(async () => {
    const map = new mapbox.Map({
      container: "map_base", // container ID
      center: [10.4515, 51.1657], // starting position [lng, lat]
      zoom: 5.7, // starting zoom,
      accessToken:
        "pk.eyJ1Ijoia2FsZGVyYS1hYSIsImEiOiJjbDh0ejgycG8wNnpxM3duYnMwanUxeXJ3In0.P6Gw61fus39Y3C_WYDbkdA",
    });
    mapbox.Style.setSourceType(
      mapboxPmTiles.SOURCE_TYPE,
      mapboxPmTiles.PmTilesSource
    );

    map.on("load", () => {
 map.addSource('municipalities_source', {
			type: mapboxPmTiles.PmTilesSource.SOURCE_TYPE,
			url: municipalities_pmtiles,
			generateId: true 
		});

		map.addLayer({
			id: 'municipalities',
			source: 'municipalities_source',
			'source-layer': 'gemeinden_simplify200',
			type: 'line',
			paint: {
				'line-color': '#ff703b', // blue color fill
				'line-width': 1, // blue color fill
				'line-opacity': 0.3
			}
		});

		map.addLayer({
			id: 'municipalities-fill',
			source: 'municipalities_source',
			'source-layer': 'gemeinden_simplify200',
			type: 'fill',
			paint: {
				'fill-color': '#ff703b', // blue color fill
				'fill-opacity': ['case', ['boolean', ['feature-state', 'hover'], false], 0.5, 0]
			}
		});
    });
    map.on('mousemove', 'municipalities-fill', (e) => {
		if (e.features.length > 0) {
			if (hoveredPolygonId !== null) {
				map.setFeatureState(
					{
						source: 'municipalities_source',
						sourceLayer: 'municipalities-fill',
						id: hoveredPolygonId
					},
					{ hover: false }
				);
			}
			console.log(e.features[0]);
			hoveredPolygonId = e.features[0].id;
			map.setFeatureState(
				{
					source: 'municipalities_source',
					sourceLayer: 'municipalities-fill',
					id: hoveredPolygonId
				},
				{ hover: true }
			);
		}
	});

	// When the mouse leaves the state-fill layer, update the feature state of the
	// previously hovered feature.
	map.on('mouseleave', 'municipalities-fills', () => {
		if (hoveredPolygonId !== null) {
			map.setFeatureState(
				{
					source: 'municipalities_source',
					sourceLayer: 'municipalities-fill',
					id: hoveredPolygonId
				},
				{ hover: false }
			);
		}
		hoveredPolygonId = null;
	});
  });
</script>

<div id="map_base">
  {#if map}
    <slot />
  {/if}
</div>

<style>
  #map_base {
    position: absolute;
    top: 0;
    bottom: 0;
    width: 100%;
  }
</style>
