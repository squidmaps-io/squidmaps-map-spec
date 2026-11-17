# The squidmaps map spec

The squidmaps map spec is an open JSON format that describes a whole web map
in one document: its data, styles, popups, legend, basemap and view. Its
features are [GeoJSON](https://datatracker.ietf.org/doc/html/rfc7946), so any
GeoJSON tool can read them, and their styles are properties whose keys start
with `@`, so styling travels with each feature.

It's the format [squidmaps](https://squidmaps.io) saves, imports and exports
maps in, and the one AI assistants write when they make a map with the
[squidmaps MCP server](https://squidmaps.io/docs/mcp) or the
[ephemeral maps API](https://squidmaps.io/docs/api/ephemeral-maps).

## A map

```json
{
  "name": "Coffee in the East Village",
  "description": "Where to get a **good cup**.",
  "basemap": { "type": "SQUIDMAPS", "style": "light", "opacity": 1, "labelVisibility": true },
  "extent": { "zoom": { "mode": "range", "min": 0, "max": 20 } },
  "view": { "center": [-73.985, 40.727], "zoom": 15 },
  "legend": null,
  "features": [
    {
      "type": "Feature",
      "id": "abraco",
      "geometry": { "type": "Point", "coordinates": [-73.986645, 40.726721] },
      "properties": {
        "name": "Abraço",
        "@fill": "#D9664E",
        "@radius": 10,
        "@icon": "solid/mug-hot",
        "@tooltip": true
      }
    }
  ]
}
```

## What's here

- **[`schema/map.schema.json`](schema/map.schema.json)**: the spec as
  [JSON Schema](https://json-schema.org) (draft 2020-12), to check a map
  against, or to generate types from. Tools can fetch it from
  <https://raw.githubusercontent.com/squidmaps-io/squidmaps-map-spec/main/schema/map.schema.json>.
- **[`docs/`](docs/overview.md)**: the spec, written out:
  - [Overview](docs/overview.md): what's in a map, and a complete example
  - [Map](docs/map.md): the map's own fields, like its basemap, extent and legend
  - [Features](docs/features.md): features, and every `@` style property
- **[`examples/`](examples/)**: complete maps that match the spec, one for each
  kind of feature and style in [`examples/features/`](examples/features/).

## Checking a map

Any JSON Schema validator that supports draft 2020-12 works. With
[ajv-cli](https://github.com/ajv-validator/ajv-cli):

```sh
npx -p ajv-cli -p ajv-formats ajv validate --spec=draft2020 --strict=false \
  -c ajv-formats -s schema/map.schema.json -d my-map.json
```

To see a map, [import it](https://squidmaps.io/docs/importing#squidmaps-maps)
into squidmaps.

## About this repo

The spec is maintained in squidmaps' own code, and this repo is generated
from it whenever it changes, so pull requests here can't be merged. To
report a problem or suggest a change, open an
[issue](https://github.com/squidmaps-io/squidmaps-map-spec/issues), or write
to support@squidmaps.io.

squidmaps is in beta, so the spec may still change.

## License

[MIT](LICENSE)
