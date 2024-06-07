<script>
  import { onMount, setContext } from "svelte";
  import { mapbox, key } from "./store.js";
  import "mapbox-gl/dist/mapbox-gl.css";
  import * as mapboxPmTiles from "mapbox-pmtiles";

  let map;
  let accessToken = import.meta.env.VITE_MAPBOX_API_ACCESS_TOKEN;

  setContext(key, {
    getMap: () => map,
  });

  const PMTILES_URL =
    "https://r2-public.protomaps.com/protomaps-sample-datasets/protomaps-basemap-opensource-20230408.pmtiles";

  onMount(async () => {
    const header = await mapboxPmTiles.PmTilesSource.getHeader(PMTILES_URL);
    const map = new mapbox.Map({
      container: "map_base", // container ID
      center: [-74.5, 40], // starting position [lng, lat]
      zoom: 9, // starting zoom,
      accessToken:
        "pk.eyJ1IjoibWFqaWRob2phdGlyZWFkeSIsImEiOiJjbHJxbXZvZDEwMDJhMmtuMmx6NHEwYTV2In0.eLlTQdMMrimVg9NxacXFmg",
    });
    mapbox.Style.setSourceType(
      mapboxPmTiles.SOURCE_TYPE,
      mapboxPmTiles.PmTilesSource
    );

    map.on("load", () => {
      const PMTILES_URL =
        "https://r2-public.protomaps.com/protomaps-sample-datasets/protomaps-basemap-opensource-20230408.pmtiles";

      map.addSource("pmTileSourceName", {
        type: mapboxPmTiles.SOURCE_TYPE, //Add this line
        url: PMTILES_URL,
      });

      map.showTileBoundaries = true;
      map.addLayer({
        id: "places",
        source: "pmTileSourceName",
        "source-layer": "places",
        type: "circle",
        paint: {
          "circle-color": "steelblue",
        },
        maxzoom: 14,
      });
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
