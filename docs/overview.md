# Map spec overview

_Last updated October 24, 2026_

> The squidmaps map spec is an open JSON format that describes a whole map in one document: its data, styles, popups, legend, basemap and view. Its features are GeoJSON, so any GeoJSON tool can read them.

> **Beta:** squidmaps is in beta. Features, the map spec and these docs may change without notice, and things may occasionally break. Export a copy of any map that matters to you, and tell us when something isn't right: use Beta feedback, or email support@squidmaps.io.

## What's in a map

Every squidmaps map is one JSON document: its name and description, its
basemap, how far visitors can zoom and pan, its legend, and its features as
GeoJSON, styled by their properties. A document that doesn't match the spec
isn't a valid map.

- **The map** holds its own fields. See [Map](map.md).
- **Features** are GeoJSON Features with an `id`. Their details,
  interactions and styles are in their `properties`, with styles under keys
  that start with `@`, so styling travels with the feature. See
  [Features](features.md).

## The whole map in one document

A web map is usually spread across several places: its data in GeoJSON, its
look in a style file that refers to the data by name, and its title, legend,
popups and starting view in app code. The map spec puts all of it in one
document, so:

- a map is one file to keep, export, import or send
- a script or an AI assistant can write a whole map at once, and check it
  against the spec's [JSON Schema](https://github.com/squidmaps-io/squidmaps-map-spec/blob/main/schema/map.schema.json)
- the [ephemeral maps API](https://squidmaps.io/docs/api/ephemeral-maps) takes a whole map in one
  request

### Composable

A feature's style, and what it shows when someone hovers over or clicks it,
are in its own `properties`. So maps are composable: put features from two
maps into one, and each keeps its look. Joining their `features` lists is all
it takes. The map's own fields, like its legend, belong to one map, so choose
which to keep.

## A complete example

A map with a café and a park, each styled, and a legend for both: first as
visitors see it, then as its document.

```json
{
  "name": "Coffee in the East Village",
  "description": "Where to get a **good cup**, and a park to drink it in.",
  "basemap": {
    "type": "SQUIDMAPS",
    "style": "light",
    "opacity": 1,
    "labelVisibility": true
  },
  "extent": {
    "zoom": {
      "mode": "range",
      "min": 12,
      "max": 18
    },
    "bounds": [
      -74.02,
      40.7,
      -73.95,
      40.75
    ],
    "initialBounds": [
      -74,
      40.715,
      -73.97,
      40.74
    ],
    "scrollZoom": true
  },
  "view": {
    "center": [
      -73.985,
      40.727
    ],
    "zoom": 15
  },
  "legend": {
    "title": "Legend",
    "rows": [
      {
        "label": "Cafés",
        "symbol": {
          "type": "circle",
          "fill": "#D9664E",
          "radius": 10,
          "stroke": "#FFFFFF",
          "strokeWidth": 2,
          "icon": "solid/mug-hot",
          "iconColor": null
        }
      },
      {
        "label": "Tompkins Square Park",
        "symbol": {
          "type": "area",
          "color": "#2F8A4E",
          "width": 2,
          "opacity": 1,
          "dasharray": [],
          "cap": "round",
          "fillColor": "#2F8A4E",
          "fillOpacity": 0.3
        }
      }
    ]
  },
  "panel": {
    "visible": true,
    "featureList": true,
    "search": false
  },
  "controls": {
    "zoom": true,
    "fullscreen": false
  },
  "features": [
    {
      "type": "Feature",
      "id": "abraco",
      "geometry": {
        "type": "Point",
        "coordinates": [
          -73.986645,
          40.726721
        ]
      },
      "properties": {
        "name": "Abraço",
        "description": "Tiny, standing room only. Get the olive oil cake.",
        "@folder": "Cafés",
        "@point-type": "circle",
        "@fill": "#D9664E",
        "@radius": 10,
        "@icon": "solid/mug-hot",
        "@show-label": true,
        "@tooltip": true,
        "@popup": "card",
        "@directions": true
      }
    },
    {
      "type": "Feature",
      "id": "tompkins",
      "geometry": {
        "type": "Polygon",
        "coordinates": [
          [
            [
              -73.982288,
              40.727809
            ],
            [
              -73.979992,
              40.726849
            ],
            [
              -73.981247,
              40.725085
            ],
            [
              -73.983543,
              40.726028
            ],
            [
              -73.982288,
              40.727809
            ]
          ]
        ]
      },
      "properties": {
        "name": "Tompkins Square Park",
        "@line-color": "#2F8A4E",
        "@fill-color": "#2F8A4E",
        "@fill-opacity": 0.3,
        "@popup": "panel"
      }
    }
  ]
}
```

## Generated from the spec

These pages are generated from the spec's source, so they always describe
the current version. The same spec is published on GitHub, in
[squidmaps-io/squidmaps-map-spec](https://github.com/squidmaps-io/squidmaps-map-spec), as
[JSON Schema](https://github.com/squidmaps-io/squidmaps-map-spec/blob/main/schema/map.schema.json) for validating maps in other tools, with example
maps.

## Reference

- [Map](map.md): The map's own fields: name, basemap, extent, view, legend and panel.
- [Features](features.md): Geometry, common properties, and how points, lines, areas, text and images are drawn.

To send a map to squidmaps from your own code, see the [API](https://squidmaps.io/docs/api).
