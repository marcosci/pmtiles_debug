<script>
  import * as mapboxPmTiles from "mapbox-pmtiles";

  mapbox.Style.setSourceType(
    mapboxPmTiles.SOURCE_TYPE,
    mapboxPmTiles.PmTilesSource
  );

  map.on("load", () => {
	map.addSource('municipalities_source', {
		type: mapboxPmTiles.PmTilesSource.SOURCE_TYPE,
		url: pmtiles,
		generateId: true 
	});

	map.addLayer({
		id: 'municipalities',
		source: 'municipalities_source',
		'source-layer': source_layer,
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
		'source-layer': source_layer,
		type: 'fill',
		paint: {
			'fill-color': '#ff703b', // blue color fill
			'fill-opacity': ['case', ['boolean', ['feature-state', 'hover'], false], 1, 0]
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
</script>