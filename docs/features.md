# Features

_Last updated November 12, 2026_

> Each feature is a GeoJSON Feature with a string id. Its properties hold its details, what visitors get from it, and how it's drawn.

> **Beta:** squidmaps is in beta. Features, the map spec and these docs may change without notice, and things may occasionally break. Export a copy of any map that matters to you, and tell us when something isn't right: use Beta feedback, or email support@squidmaps.io.

## Geometry

Points, lines and polygons, their Multi- forms, and geometry collections, as
in [GeoJSON](https://datatracker.ietf.org/doc/html/rfc7946#section-3.1).
Coordinates are `[longitude, latitude]`, with at most six decimal places. A
Point can also be [text](#feature-text), and a Polygon of four corners an
[image](#feature-image). A Polygon can also be a
[circle](#feature-circle-area) or a [rectangle](#feature-rectangle-area),
made from its center and radius, or its center, size and rotation. A
MultiLineString can be a [freehand drawing](#feature-freehand), a line per
stroke, and a LineString of two positions an [arrow](#feature-arrow).

## Order

A map's `features` are listed top first, as in the editor's list. Points
and text are drawn above lines, polygons, freehand drawings, arrows and images,
whatever their place. Among points and text, and among lines, polygons,
drawings and images, features earlier in the list are drawn on top. One exception keeps outlines readable: between
two images, polygons' fills are drawn under the lines and outlines listed with
them. Lines' and polygons' labels are drawn above every line, polygon and image,
under points and text.

## Properties

Everything about a feature is in its GeoJSON `properties`: its own data,
like `name`, and how it looks and behaves, under keys starting with `@`, so
styling travels with the feature.

Every property is optional: a feature without one gets its default, so a
plain GeoJSON point is drawn as the default circle. A value that doesn't
match its property makes the feature, and its map, invalid. Properties
squidmaps doesn't use are kept as they are.

## Common properties

What a feature is, and what visitors get from it on the published map.

### Details

**All features.** What the feature is. Other properties without an "@" are the feature's own data, kept as they are.

| Property | Type | Default | Description |
| --- | --- | --- | --- |
| `name` | `string` | None | Shown in its tooltip, popup and label, and suggested for its legend row. Text's name is its words, drawn on the map. |
| `description` | `string` | None | Markdown, shown in its popup. HTML is escaped, and links open in a new tab. |
| `@folder` | `string` (1 to 100 characters) | None | The folder it's in, by name. Folders are one level deep, and exist while a feature names them. |
| `@images` | `array of FeatureImage` (at most 24 items) | None | Its images, cover first: at most 24, uploaded in the editor. |
| `@locked` | `boolean` | `false` | Locked in the editor: it can be selected, but not moved, reshaped, restyled or deleted until it's unlocked. Visitors' maps ignore it. |
| `@hidden` | `boolean` | `false` | Hidden: kept in the map, but not drawn. The editor lists it with its eye closed, and the published map and its preview leave it out. |

### Tooltip

**Points, lines and polygons.** A small box following the pointer while visitors hover over the feature.

| Property | Type | Default | Description |
| --- | --- | --- | --- |
| `@tooltip` | `boolean` | `false` | Whether hovering over the feature shows a tooltip. |
| `@tooltip-name` | `boolean` | `true` | Show the feature's name. |
| `@tooltip-thumbnail` | `boolean` | `false` | Show a thumbnail of its first image, if it has one. |
| `@tooltip-background` | `Color` | `"#000000"` | The tooltip's color. |
| `@tooltip-color` | `Color` | `"#FFFFFF"` | The text's color. |

```json
{
  "type": "Feature",
  "id": "area-1",
  "geometry": {
    "type": "Polygon",
    "coordinates": [
      [
        [-73.9564, 40.773816],
        [-73.9546, 40.773816],
        [-73.9546, 40.775184],
        [-73.9564, 40.775184],
        [-73.9564, 40.773816]
      ]
    ]
  },
  "properties": {
    "name": "Carl Schurz Park",
    "@tooltip": true,
    "@tooltip-background": "#2F8A4E",
    "@line-color": "#2F8A4E",
    "@fill-color": "#2F8A4E"
  }
}
```

### Popup

**Points, lines and polygons.** What visitors get when they click the feature.

| Property | Type | Default | Description |
| --- | --- | --- | --- |
| `@popup` | `"card" \| "panel" \| "none"` | `"card"` | What clicking the feature opens: a card above it with its images, name and description; the same in a panel beside the map; or nothing. |
| `@directions` | `boolean` | `false` | For points: offer directions to it, in Google Maps, from its popup. |

```json
{
  "type": "Feature",
  "id": "cafe",
  "geometry": {
    "type": "Point",
    "coordinates": [-73.9555, 40.7745]
  },
  "properties": {
    "name": "Café on the corner",
    "description": "Get the **cardamom bun**, and a seat by the window.",
    "@popup": "card",
    "@fill": "#D9664E",
    "@radius": 11,
    "@icon": "solid/mug-hot"
  }
}
```

## Points

Points are drawn as 4 types (circle, emoji, marker and custom), chosen by `@point-type`. Each type has
its own properties, so a point keeps every type's style and changing its
type back restores it.

### Point type

**Points.** How a point is drawn. Each type keeps its own style properties, so changing a point's type and back restores its style.

| Property | Type | Default | Description |
| --- | --- | --- | --- |
| `@point-type` | `"circle" \| "emoji" \| "marker" \| "custom"` | `"circle"` | Which of the point styles below it's drawn with. |

```json
[
  {
    "type": "Feature",
    "id": "point-1",
    "geometry": {
      "type": "Point",
      "coordinates": [-73.9579, 40.7745]
    },
    "properties": {
      "name": "A circle"
    }
  },
  {
    "type": "Feature",
    "id": "point-2",
    "geometry": {
      "type": "Point",
      "coordinates": [-73.9555, 40.7745]
    },
    "properties": {
      "name": "A marker pin",
      "@point-type": "marker"
    }
  },
  {
    "type": "Feature",
    "id": "point-3",
    "geometry": {
      "type": "Point",
      "coordinates": [-73.9531, 40.7745]
    },
    "properties": {
      "name": "An emoji",
      "@point-type": "emoji",
      "@emoji": "🌳"
    }
  }
]
```

### Circles

**Points.** A colored circle, optionally with an icon in it. For points with "@point-type": "circle".

| Property | Type | Default | Description |
| --- | --- | --- | --- |
| `@fill` | `Color` | `"#3F80C4"` | The circle's color. |
| `@radius` | `number` (2 to 40) | `8` | In pixels, not counting the stroke. |
| `@stroke` | `Color` | `"#FFFFFF"` | The ring around the circle. |
| `@stroke-width` | `number` (0 to 10) | `2` | The ring's width in pixels, outside the radius. |
| `@icon` | `Icon` | None | A Font Awesome icon in the circle. |
| `@icon-color` | `Color` | None | The icon's color. Without one, white or dark ink, whichever contrasts the fill. |

```json
[
  {
    "type": "Feature",
    "id": "circle-1",
    "geometry": {
      "type": "Point",
      "coordinates": [-73.9591, 40.7745]
    },
    "properties": {}
  },
  {
    "type": "Feature",
    "id": "circle-2",
    "geometry": {
      "type": "Point",
      "coordinates": [-73.9567, 40.7745]
    },
    "properties": {
      "@fill": "#D9664E",
      "@radius": 13,
      "@icon": "solid/mug-hot"
    }
  },
  {
    "type": "Feature",
    "id": "circle-3",
    "geometry": {
      "type": "Point",
      "coordinates": [-73.9543, 40.7745]
    },
    "properties": {
      "@fill": "#12312C",
      "@radius": 5,
      "@stroke": "#F2C14E",
      "@stroke-width": 4
    }
  },
  {
    "type": "Feature",
    "id": "circle-4",
    "geometry": {
      "type": "Point",
      "coordinates": [-73.9519, 40.7745]
    },
    "properties": {
      "@fill": "#F2C14E",
      "@radius": 18,
      "@stroke-width": 0,
      "@icon": "solid/star",
      "@icon-color": "#8A3B12"
    }
  }
]
```

### Emoji

**Points.** An emoji, drawn as its Twemoji image. For points with "@point-type": "emoji".

| Property | Type | Default | Description |
| --- | --- | --- | --- |
| `@emoji` | `Emoji` | `"📍"` | One emoji, drawn as its Twemoji image. |
| `@size` | `number` (12 to 64) | `28` | The emoji's width and height in pixels. |

```json
[
  {
    "type": "Feature",
    "id": "emoji-1",
    "geometry": {
      "type": "Point",
      "coordinates": [-73.9579, 40.7745]
    },
    "properties": {
      "@point-type": "emoji",
      "@emoji": "🍕",
      "@size": 20
    }
  },
  {
    "type": "Feature",
    "id": "emoji-2",
    "geometry": {
      "type": "Point",
      "coordinates": [-73.9555, 40.7745]
    },
    "properties": {
      "@point-type": "emoji",
      "@emoji": "🚲",
      "@size": 32
    }
  },
  {
    "type": "Feature",
    "id": "emoji-3",
    "geometry": {
      "type": "Point",
      "coordinates": [-73.9531, 40.7745]
    },
    "properties": {
      "@point-type": "emoji",
      "@emoji": "🏛️",
      "@size": 48
    }
  }
]
```

### Marker pins

**Points.** A map pin standing on the point, with an optional icon in its head. For points with "@point-type": "marker".

| Property | Type | Default | Description |
| --- | --- | --- | --- |
| `@marker-shell` | `Color` | `"#FFFFFF"` | The pin's outer color. |
| `@marker-disc` | `Color` | `"#0E9384"` | The disc in the pin's head. |
| `@marker-icon` | `Icon` | None | A Font Awesome icon on the disc. Without one, it's empty. |
| `@marker-icon-color` | `Color` | None | The icon's color. Without one, white or dark ink, whichever contrasts the disc. |
| `@marker-size` | `number \| "small" \| "medium" \| "large"` | `34` | The pin's width in pixels, 16 to 96; it's 1.14 times as tall. Or a preset: small, medium or large. |

```json
[
  {
    "type": "Feature",
    "id": "marker-1",
    "geometry": {
      "type": "Point",
      "coordinates": [-73.9579, 40.7745]
    },
    "properties": {
      "@point-type": "marker"
    }
  },
  {
    "type": "Feature",
    "id": "marker-2",
    "geometry": {
      "type": "Point",
      "coordinates": [-73.9555, 40.7745]
    },
    "properties": {
      "@point-type": "marker",
      "@marker-shell": "#C2473A",
      "@marker-disc": "#FFFFFF",
      "@marker-icon": "solid/utensils",
      "@marker-size": 48
    }
  },
  {
    "type": "Feature",
    "id": "marker-3",
    "geometry": {
      "type": "Point",
      "coordinates": [-73.9531, 40.7745]
    },
    "properties": {
      "@point-type": "marker",
      "@marker-shell": "#12312C",
      "@marker-disc": "#F2C14E",
      "@marker-icon": "solid/star",
      "@marker-icon-color": "#12312C"
    }
  }
]
```

### Labels

**Points, lines and polygons.** A feature's name, or its own label text, drawn on the map: beside a point or centered on it, along a line, or inside a polygon. A line's label follows its line, repeated along a long one, where there's room for it. A polygon's label sits at the point inside it farthest from its edges, which for a long or bent polygon isn't its centroid, unless it's been moved somewhere else inside it. A point's label belongs to the point, not its type.

| Property | Type | Default | Description |
| --- | --- | --- | --- |
| `@show-label` | `boolean` | `false` | Whether the feature's label (its name, or its label text) is drawn: beside or on a point, along a line, or inside an area. Labels that would overlap are hidden until there's room, unless they ignore conflicts. |
| `@label-text` | `string \| number` | None | What the label says, when it isn't the feature's name: a count on a cluster map, say, a short code, or a route number. A number is shown as written. Without one, or when it's blank, the label is the name. |
| `@label-size` | `number` (8 to 36) | `13` | The text's size in pixels. |
| `@label-color` | `Color` | `"#12312C"` | The text's color. Its halo, when it has one, is white or dark ink, to contrast. |
| `@label-placement` | `"top" \| "right" \| "center"` | `"top"` | Points only: centered above the point, to its right, or centered on it. "center" suits a short label like a count on a circle: size the circle to fit it. A line's label follows the line, and an area's sits inside it, where there's most room. |
| `@label-weight` | `"regular" \| "semibold" \| "bold" \| "black"` | `"bold"` | How heavy the text is. Lines' and areas' labels come in two weights: regular, and a medium weight for the rest. |
| `@label-offset` | `number` (-30 to 30) | `0` | Lines only: how far the label sits from its line, in pixels. Positive is above the line as the label reads, negative below, and 0 on the line, over it. Beside a thick line, use at least half its width plus half the text's size. |
| `@label-halo` | `boolean` | `true` | Whether the text has a halo in white or dark ink, to stand out on the basemap. A label centered on a circle reads better without one. |
| `@label-ignore-conflicts` | `boolean` | `false` | Whether the label always shows, even where it overlaps other labels or points. A line's label still needs a stretch of line long enough to hold it. |
| `@label-point` | `[number, number]` (items in turn: -180 to 180; -85.051129 to 85.051129) | None | Areas only: where the label is centered, as [longitude, latitude], instead of the point inside the area farthest from its edges. It has to be inside the area (in any of a MultiPolygon's parts, and not in a hole); if it isn't, say after the area is reshaped, the label goes back to its automatic place. |

```json
[
  {
    "type": "Feature",
    "id": "label-1",
    "geometry": {
      "type": "Point",
      "coordinates": [-73.9579, 40.7745]
    },
    "properties": {
      "name": "Above",
      "@show-label": true
    }
  },
  {
    "type": "Feature",
    "id": "label-2",
    "geometry": {
      "type": "Point",
      "coordinates": [-73.9555, 40.7745]
    },
    "properties": {
      "name": "To the right",
      "@show-label": true,
      "@label-placement": "right"
    }
  },
  {
    "type": "Feature",
    "id": "label-3",
    "geometry": {
      "type": "Point",
      "coordinates": [-73.9531, 40.7745]
    },
    "properties": {
      "name": "Bigger, black, in color",
      "@show-label": true,
      "@label-size": 17,
      "@label-weight": "black",
      "@label-color": "#C2473A",
      "@fill": "#C2473A"
    }
  }
]
```

A label centered on its point suits a short label like a count, for a
cluster map. Here each point keeps its name, for its popup, and labels its
count with `@label-text`; each circle is sized to hold it, without a halo.

```json
[
  {
    "type": "Feature",
    "id": "cluster-1",
    "geometry": {
      "type": "Point",
      "coordinates": [-73.9597, 40.7751]
    },
    "properties": {
      "name": "Carl Schurz Park",
      "@label-text": 7,
      "@radius": 13,
      "@fill": "#3F80C4",
      "@stroke-width": 3,
      "@show-label": true,
      "@label-placement": "center",
      "@label-size": 10,
      "@label-weight": "bold",
      "@label-color": "#FFFFFF",
      "@label-halo": false
    }
  },
  {
    "type": "Feature",
    "id": "cluster-2",
    "geometry": {
      "type": "Point",
      "coordinates": [-73.9571, 40.7737]
    },
    "properties": {
      "name": "East 79th Street",
      "@label-text": 42,
      "@radius": 18,
      "@fill": "#E08A2E",
      "@stroke-width": 3,
      "@show-label": true,
      "@label-placement": "center",
      "@label-size": 14,
      "@label-weight": "bold",
      "@label-color": "#FFFFFF",
      "@label-halo": false
    }
  },
  {
    "type": "Feature",
    "id": "cluster-3",
    "geometry": {
      "type": "Point",
      "coordinates": [-73.9543, 40.7752]
    },
    "properties": {
      "name": "Lexington Avenue",
      "@label-text": 186,
      "@radius": 24,
      "@fill": "#C2473A",
      "@stroke-width": 3,
      "@show-label": true,
      "@label-placement": "center",
      "@label-size": 18,
      "@label-weight": "black",
      "@label-color": "#FFFFFF",
      "@label-halo": false
    }
  },
  {
    "type": "Feature",
    "id": "cluster-4",
    "geometry": {
      "type": "Point",
      "coordinates": [-73.9517, 40.774]
    },
    "properties": {
      "name": "York Avenue",
      "@label-text": 23,
      "@radius": 16,
      "@fill": "#E08A2E",
      "@stroke-width": 3,
      "@show-label": true,
      "@label-placement": "center",
      "@label-size": 12,
      "@label-weight": "bold",
      "@label-color": "#FFFFFF",
      "@label-halo": false
    }
  }
]
```

## Lines

**Lines.** How a line is drawn. When a property is missing, its simplestyle-spec equivalent is read instead, so GeoJSON styled for other tools keeps its look.

| Property | Type | Default | Description |
| --- | --- | --- | --- |
| `@line-color` | `Color` | `"#3F80C4"` | The line's color. Falls back to `stroke`. |
| `@line-width` | `number` (0.5 to 20) | `3` | In pixels. Falls back to `stroke-width`. |
| `@line-opacity` | `number` (0 to 1) | `1` | From 0 (invisible) to 1 (opaque). Falls back to `stroke-opacity`. |
| `@line-dasharray` | `array of number` (at most 8 items, each 0 to 100) | `[]` | Dash and gap lengths in turn, in line widths, like [4, 2]. Empty is solid. |
| `@line-cap` | `"butt" \| "round" \| "square"` | `"round"` | How the line's ends are drawn. |

```json
[
  {
    "type": "Feature",
    "id": "line-1",
    "geometry": {
      "type": "LineString",
      "coordinates": [
        [-73.96, 40.77565],
        [-73.957, 40.77605],
        [-73.954, 40.77565],
        [-73.951, 40.77605]
      ]
    },
    "properties": {
      "@line-color": "#3F80C4",
      "@line-width": 6
    }
  },
  {
    "type": "Feature",
    "id": "line-2",
    "geometry": {
      "type": "LineString",
      "coordinates": [
        [-73.96, 40.77475],
        [-73.957, 40.77515],
        [-73.954, 40.77475],
        [-73.951, 40.77515]
      ]
    },
    "properties": {
      "@line-color": "#C2473A",
      "@line-width": 3,
      "@line-dasharray": [4, 2],
      "@line-cap": "butt"
    }
  },
  {
    "type": "Feature",
    "id": "line-3",
    "geometry": {
      "type": "LineString",
      "coordinates": [
        [-73.96, 40.77385],
        [-73.957, 40.77425],
        [-73.954, 40.77385],
        [-73.951, 40.77425]
      ]
    },
    "properties": {
      "@line-color": "#12312C",
      "@line-width": 4,
      "@line-dasharray": [0, 2]
    }
  },
  {
    "type": "Feature",
    "id": "line-4",
    "geometry": {
      "type": "LineString",
      "coordinates": [
        [-73.96, 40.77295],
        [-73.957, 40.77335],
        [-73.954, 40.77295],
        [-73.951, 40.77335]
      ]
    },
    "properties": {
      "@line-color": "#2F8A4E",
      "@line-width": 10,
      "@line-opacity": 0.5
    }
  }
]
```

A line takes the same [label properties](#feature-label) as a point, except
`@label-placement`: its label follows the line, upright, and repeats along a
long one. A label shows where a stretch of line is long and straight enough
to hold it, so a short or tightly bent line may not show it at every zoom.
`@label-offset`, for lines only, moves the label off the line, in pixels:
positive above the line as the label reads, negative below. A label stays on
the same side as it reads whichever way the line was drawn. To clear a thick
line, offset it by at least half the line's width plus half the label's
size. Here the greenway's label sits above its line, one line is labeled
with `@label-text`, a route number, keeping its full name for its popup, and
the others are labeled on their lines, over them.

```json
[
  {
    "type": "Feature",
    "id": "curve-1",
    "geometry": {
      "type": "LineString",
      "coordinates": [
        [-73.9615, 40.7754],
        [-73.9605, 40.77555],
        [-73.9595, 40.77566],
        [-73.9585, 40.7757],
        [-73.9575, 40.77566],
        [-73.9565, 40.77555],
        [-73.9555, 40.7754],
        [-73.9545, 40.77525],
        [-73.9535, 40.77514],
        [-73.9525, 40.7751],
        [-73.9515, 40.77514],
        [-73.9505, 40.77525],
        [-73.9495, 40.7754]
      ]
    },
    "properties": {
      "name": "East River Greenway",
      "@show-label": true,
      "@line-color": "#2F8A4E",
      "@line-width": 5,
      "@label-offset": 10,
      "@label-color": "#1F5E35"
    }
  },
  {
    "type": "Feature",
    "id": "curve-2",
    "geometry": {
      "type": "LineString",
      "coordinates": [
        [-73.9615, 40.774752],
        [-73.9605, 40.7748],
        [-73.9595, 40.774767],
        [-73.9585, 40.774662],
        [-73.9575, 40.774514],
        [-73.9565, 40.774362],
        [-73.9555, 40.774248],
        [-73.9545, 40.7742],
        [-73.9535, 40.774233],
        [-73.9525, 40.774338],
        [-73.9515, 40.774486],
        [-73.9505, 40.774638],
        [-73.9495, 40.774752]
      ]
    },
    "properties": {
      "name": "M15 Select Bus Service",
      "@label-text": "M15",
      "@show-label": true,
      "@line-color": "#3F80C4",
      "@line-width": 4,
      "@label-color": "#3F80C4",
      "@label-size": 15
    }
  },
  {
    "type": "Feature",
    "id": "curve-3",
    "geometry": {
      "type": "LineString",
      "coordinates": [
        [-73.9615, 40.773873],
        [-73.9605, 40.773774],
        [-73.9595, 40.773628],
        [-73.9585, 40.773475],
        [-73.9575, 40.773355],
        [-73.9565, 40.773301],
        [-73.9555, 40.773327],
        [-73.9545, 40.773426],
        [-73.9535, 40.773572],
        [-73.9525, 40.773725],
        [-73.9515, 40.773845],
        [-73.9505, 40.773899],
        [-73.9495, 40.773873]
      ]
    },
    "properties": {
      "name": "Proposed bike lane",
      "@show-label": true,
      "@line-color": "#C2473A",
      "@line-width": 3,
      "@line-dasharray": [2, 2],
      "@label-color": "#C2473A",
      "@label-size": 12,
      "@label-weight": "regular"
    }
  }
]
```

## Polygons

**Polygons.** How a polygon is drawn: its outline, like a line's, and its fill. Missing properties fall back to simplestyle-spec too.

| Property | Type | Default | Description |
| --- | --- | --- | --- |
| `@line-color` | `Color` | `"#3F80C4"` | The line's color. Falls back to `stroke`. |
| `@line-width` | `number` (0.5 to 20) | `2` | In pixels. Falls back to `stroke-width`. |
| `@line-opacity` | `number` (0 to 1) | `1` | From 0 (invisible) to 1 (opaque). Falls back to `stroke-opacity`. |
| `@line-dasharray` | `array of number` (at most 8 items, each 0 to 100) | `[]` | Dash and gap lengths in turn, in line widths, like [4, 2]. Empty is solid. |
| `@line-cap` | `"butt" \| "round" \| "square"` | `"round"` | How the line's ends are drawn. |
| `@fill-color` | `Color` | `"#3F80C4"` | The area's inside color. Falls back to `fill`. |
| `@fill-opacity` | `number` (0 to 1) | `0.3` | From 0 (no fill) to 1 (opaque). Falls back to `fill-opacity`. |

```json
[
  {
    "type": "Feature",
    "id": "area-1",
    "geometry": {
      "type": "Polygon",
      "coordinates": [
        [
          [-73.9592, 40.773816],
          [-73.9574, 40.773816],
          [-73.9574, 40.775184],
          [-73.9592, 40.775184],
          [-73.9592, 40.773816]
        ]
      ]
    },
    "properties": {}
  },
  {
    "type": "Feature",
    "id": "area-2",
    "geometry": {
      "type": "Polygon",
      "coordinates": [
        [
          [-73.9564, 40.773816],
          [-73.9546, 40.773816],
          [-73.9546, 40.775184],
          [-73.9564, 40.775184],
          [-73.9564, 40.773816]
        ]
      ]
    },
    "properties": {
      "@line-color": "#2F8A4E",
      "@line-width": 1,
      "@fill-color": "#2F8A4E",
      "@fill-opacity": 0.6
    }
  },
  {
    "type": "Feature",
    "id": "area-3",
    "geometry": {
      "type": "Polygon",
      "coordinates": [
        [
          [-73.9536, 40.773816],
          [-73.9518, 40.773816],
          [-73.9518, 40.775184],
          [-73.9536, 40.775184],
          [-73.9536, 40.773816]
        ]
      ]
    },
    "properties": {
      "@line-color": "#C2473A",
      "@line-width": 3,
      "@line-dasharray": [2, 2],
      "@fill-opacity": 0
    }
  }
]
```

A polygon takes the [label properties](#feature-label) too, except
`@label-placement`. Its label sits at the point inside it farthest from its
edges and holes, its pole of inaccessibility, so it stays inside a polygon of
any shape: for an L-shaped or curving polygon that isn't its centroid, which
can fall outside it. A MultiPolygon is labeled once, in its biggest part. A
long label wraps onto more lines. Circles and rectangles are labeled the
same way.

```json
[
  {
    "type": "Feature",
    "id": "area-label-1",
    "geometry": {
      "type": "Polygon",
      "coordinates": [
        [
          [-73.9615, 40.7731],
          [-73.9571, 40.7731],
          [-73.9571, 40.7743],
          [-73.9601, 40.7743],
          [-73.9601, 40.7759],
          [-73.9615, 40.7759],
          [-73.9615, 40.7731]
        ]
      ]
    },
    "properties": {
      "name": "Playground",
      "@show-label": true,
      "@line-color": "#2F8A4E",
      "@fill-color": "#2F8A4E",
      "@fill-opacity": 0.25,
      "@label-color": "#1F5E35"
    }
  },
  {
    "type": "Feature",
    "id": "area-label-2",
    "geometry": {
      "type": "Polygon",
      "coordinates": [
        [
          [-73.9561, 40.7733],
          [-73.9535, 40.7733],
          [-73.9535, 40.7757],
          [-73.9561, 40.7757],
          [-73.9561, 40.7733]
        ]
      ]
    },
    "properties": {
      "name": "John Jay Park ballfields",
      "@label-text": "Ballfields",
      "@show-label": true,
      "@line-color": "#3F80C4",
      "@fill-color": "#3F80C4",
      "@fill-opacity": 0.8,
      "@label-color": "#FFFFFF",
      "@label-halo": false,
      "@label-size": 14
    }
  },
  {
    "type": "Feature",
    "id": "area-label-3",
    "geometry": {
      "type": "Polygon",
      "coordinates": [
        [
          [-73.9525, 40.7729],
          [-73.9517, 40.7729],
          [-73.9493, 40.7761],
          [-73.9501, 40.7761],
          [-73.9525, 40.7729]
        ]
      ]
    },
    "properties": {
      "name": "Esplanade",
      "@show-label": true,
      "@line-color": "#E08A2E",
      "@fill-color": "#E08A2E",
      "@label-size": 11,
      "@label-weight": "regular"
    }
  }
]
```

To put a polygon's label somewhere else, give it `@label-point`, a
`[longitude, latitude]` inside the polygon: in any part of a MultiPolygon, but
not in a hole. A point outside the polygon, as can happen when it's reshaped,
is ignored, and the label goes back to its automatic place. Moving or
turning a polygon in the editor takes its label point with it. Here the same
L is labeled where there's most room, and with its label moved to the end of
its long leg.

```json
[
  {
    "type": "Feature",
    "id": "area-label-point-1",
    "geometry": {
      "type": "Polygon",
      "coordinates": [
        [
          [-73.9615, 40.7731],
          [-73.9563, 40.7731],
          [-73.9563, 40.7745],
          [-73.9601, 40.7745],
          [-73.9601, 40.7761],
          [-73.9615, 40.7761],
          [-73.9615, 40.7731]
        ]
      ]
    },
    "properties": {
      "name": "Automatic",
      "@show-label": true,
      "@line-color": "#2F8A4E",
      "@fill-color": "#2F8A4E",
      "@fill-opacity": 0.25,
      "@label-color": "#1F5E35"
    }
  },
  {
    "type": "Feature",
    "id": "area-label-point-2",
    "geometry": {
      "type": "Polygon",
      "coordinates": [
        [
          [-73.9551, 40.7731],
          [-73.9499, 40.7731],
          [-73.9499, 40.7745],
          [-73.9537, 40.7745],
          [-73.9537, 40.7761],
          [-73.9551, 40.7761],
          [-73.9551, 40.7731]
        ]
      ]
    },
    "properties": {
      "name": "Moved",
      "@show-label": true,
      "@line-color": "#2F8A4E",
      "@fill-color": "#2F8A4E",
      "@fill-opacity": 0.25,
      "@label-color": "#1F5E35",
      "@label-point": [-73.9511, 40.7738]
    }
  }
]
```

Lines' and polygons' labels come in two weights: `"regular"`, and a medium
weight for `"semibold"`, `"bold"` and `"black"`. Like points' labels, they
move out of each other's way, and the basemap's own labels move out of
theirs.

## Text

**Text.** Words on the map, centered on a point: a Point with "@feature-type": "text", whose name is drawn with the properties below. New lines in the name start new lines. Text isn't a kind of point, so it has no point type, label, tooltip or popup.

| Property | Type | Default | Description |
| --- | --- | --- | --- |
| `@feature-type` | `"text"` | None | Makes a Point text, drawn with the properties below. |
| `@text-size` | `number` (8 to 120) | `24` | The text's size in pixels; with "@text-scale": "map", its size at "@text-scale-zoom". |
| `@text-color` | `Color` | `"#12312C"` | The text's color. |
| `@text-halo-color` | `Color` | `"#FFFFFF"` | The outline around the letters, to keep them readable. |
| `@text-halo-width` | `number` (0 to 8) | `2` | The outline's width in pixels. 0 has none. |
| `@text-background` | `Color` | None | A rounded rectangle behind the text, in this color. Without one, there's no background. |
| `@text-background-radius` | `number` (0 to 32) | `6` | The background's corner radius in pixels. |
| `@text-scale` | `"pixels" \| "map"` | `"pixels"` | "pixels" stays the same size at every zoom; "map" grows and shrinks with the map. |
| `@text-scale-zoom` | `number` (0 to 24) | `12` | With "@text-scale": "map", the zoom at which the text is "@text-size" pixels: twice that one zoom in, half one out. |
| `@text-rotate` | `number` (-180 to 180) | `0` | How far the text is turned about its point, in degrees clockwise, from -180 to 180. 0 is level. |
| `@text-callout` | `[number, number]` (items in turn: -180 to 180; -85.051129 to 85.051129) | None | A place to point to, as [longitude, latitude]: a pointed callout joins the text to it, in the color of "@text-background". Without a background, it doesn't show. |

```json
[
  {
    "type": "Feature",
    "id": "text-1",
    "geometry": {
      "type": "Point",
      "coordinates": [-73.9589, 40.7745]
    },
    "properties": {
      "@feature-type": "text",
      "name": "Plain"
    }
  },
  {
    "type": "Feature",
    "id": "text-2",
    "geometry": {
      "type": "Point",
      "coordinates": [-73.9555, 40.7745]
    },
    "properties": {
      "@feature-type": "text",
      "name": "With a\nbackground",
      "@text-size": 16,
      "@text-color": "#FFFFFF",
      "@text-halo-width": 0,
      "@text-background": "#1E7A6C",
      "@text-callout": [-73.9555, 40.7739]
    }
  },
  {
    "type": "Feature",
    "id": "text-3",
    "geometry": {
      "type": "Point",
      "coordinates": [-73.9521, 40.7745]
    },
    "properties": {
      "@feature-type": "text",
      "name": "Turned",
      "@text-size": 20,
      "@text-color": "#C2473A",
      "@text-rotate": -20,
      "@text-scale": "map",
      "@text-scale-zoom": 16
    }
  }
]
```

## Freehand drawings

**Freehand drawings.** Marks drawn on the map as if with a pen, like an arrow or a scribble over an area: a MultiLineString with "@feature-type": "freehand", a line per stroke (or a LineString, for one stroke), drawn solid with round ends in the style below. A drawing isn't a kind of line, so it has no line style, tooltip or popup.

| Property | Type | Default | Description |
| --- | --- | --- | --- |
| `@feature-type` | `"freehand"` | None | Makes a MultiLineString a drawing, drawn with the properties below. |
| `@freehand-color` | `Color` | `"#D9664E"` | The strokes' color. |
| `@freehand-width` | `number` (1 to 40) | `4` | The strokes' width in pixels; with "@freehand-scale": "map", their width at "@freehand-scale-zoom". |
| `@freehand-opacity` | `number` (0 to 1) | `1` | From 0 (invisible) to 1 (opaque). Where strokes cross, they're no darker. |
| `@freehand-scale` | `"pixels" \| "map"` | `"map"` | "map" grows and shrinks with the map, like ink on it; "pixels" stays the same width at every zoom. |
| `@freehand-scale-zoom` | `number` (0 to 24) | `14` | With "@freehand-scale": "map", the zoom at which the strokes are "@freehand-width" pixels wide: twice that one zoom in, half one out. |

```json
[
  {
    "type": "Feature",
    "id": "freehand-1",
    "geometry": {
      "type": "MultiLineString",
      "coordinates": [
        [
          [-73.9598, 40.7739],
          [-73.9595, 40.7744],
          [-73.9591, 40.77475],
          [-73.9586, 40.77495],
          [-73.9581, 40.775]
        ],
        [
          [-73.9585, 40.7753],
          [-73.9581, 40.775],
          [-73.9584, 40.7746]
        ]
      ]
    },
    "properties": {
      "@feature-type": "freehand",
      "name": "Arrow",
      "@freehand-scale-zoom": 16
    }
  },
  {
    "type": "Feature",
    "id": "freehand-2",
    "geometry": {
      "type": "MultiLineString",
      "coordinates": [
        [
          [-73.95636, 40.77469],
          [-73.95645, 40.77454],
          [-73.95637, 40.77439],
          [-73.95627, 40.77426],
          [-73.95621, 40.7741],
          [-73.95605, 40.77399],
          [-73.95581, 40.77396],
          [-73.9556, 40.77393],
          [-73.95537, 40.77387],
          [-73.95515, 40.77393],
          [-73.95499, 40.77405],
          [-73.95481, 40.77413],
          [-73.95463, 40.77424],
          [-73.95462, 40.7744],
          [-73.95466, 40.77454],
          [-73.95463, 40.7747],
          [-73.95471, 40.77485],
          [-73.95492, 40.77493],
          [-73.9551, 40.77501],
          [-73.95529, 40.77511],
          [-73.95552, 40.77512],
          [-73.95573, 40.77505],
          [-73.95594, 40.77501],
          [-73.95617, 40.77495],
          [-73.95627, 40.77481],
          [-73.95631, 40.77466],
          [-73.95641, 40.77452],
          [-73.95643, 40.77436],
          [-73.95628, 40.77424]
        ]
      ]
    },
    "properties": {
      "@feature-type": "freehand",
      "name": "Ring",
      "@freehand-color": "#22433E",
      "@freehand-width": 3,
      "@freehand-scale": "pixels"
    }
  },
  {
    "type": "Feature",
    "id": "freehand-3",
    "geometry": {
      "type": "MultiLineString",
      "coordinates": [
        [
          [-73.953, 40.775],
          [-73.9513, 40.7749],
          [-73.9529, 40.7747],
          [-73.9512, 40.77455],
          [-73.9529, 40.77435],
          [-73.9513, 40.7742],
          [-73.9528, 40.774]
        ]
      ]
    },
    "properties": {
      "@feature-type": "freehand",
      "name": "Highlight",
      "@freehand-color": "#FFD43B",
      "@freehand-width": 16,
      "@freehand-opacity": 0.4,
      "@freehand-scale-zoom": 16
    }
  }
]
```

A drawing is drawn solid, with round ends and corners, so a stroke of one
point is a dot. A see-through one is the same shade all over, even where
its strokes cross. Drawings are decoration: visitors can't click them, and
they're left out of the legend and the map's feature list. The editor
draws one stroke at a time and doesn't reshape them; removing
`@feature-type` makes a drawing a plain line, styled as a line.

## Arrows

**Arrows.** An arrow pointing from one place to another: a LineString of two positions, its tail and then its head, with "@feature-type": "arrow". It's drawn as a shaft with a tail and a head at its second position, rounded by "@arrow-rounding" and bending by "@arrow-curve", in the style below. An arrow isn't a kind of line, so it has no line style, tooltip or popup.

| Property | Type | Default | Description |
| --- | --- | --- | --- |
| `@feature-type` | `"arrow"` | None | Makes a LineString of two positions an arrow, drawn with the properties below. |
| `@arrow-curve` | `number` (-1 to 1) | `0` | How far the middle of its shaft bows out from a straight line, as a share of its length: 0 or none is straight, positive bows to the left of the way it points (as on the map, north up) and negative to the right. 0.5 is about a half circle. |
| `@arrow-color` | `Color` | `"#D9664E"` | The arrow's color. |
| `@arrow-width` | `number` (1 to 40) | `6` | How thick its shaft is, in pixels at "@arrow-scale-zoom"; its head is sized to match. |
| `@arrow-opacity` | `number` (0 to 1) | `1` | From 0 (invisible) to 1 (opaque). |
| `@arrow-rounding` | `number` (0 to 1) | `0.5` | How rounded the three points of its head and the corners of its tail are, from 0 (a sharp head and a square tail) to 1 (as round as they go: a round tail). It's a share of the room each corner has, so an arrow looks the same at any width. |
| `@arrow-scale-zoom` | `number` (0 to 24) | `14` | The zoom at which the shaft is "@arrow-width" pixels thick. An arrow grows and shrinks with the map, like its two ends: twice as thick one zoom in, half one out. |

```json
[
  {
    "type": "Feature",
    "id": "arrow-1",
    "geometry": {
      "type": "LineString",
      "coordinates": [
        [-73.9601, 40.774],
        [-73.9577, 40.775]
      ]
    },
    "properties": {
      "@feature-type": "arrow",
      "@arrow-scale-zoom": 16,
      "name": "Curved",
      "@arrow-curve": 0.2
    }
  },
  {
    "type": "Feature",
    "id": "arrow-2",
    "geometry": {
      "type": "LineString",
      "coordinates": [
        [-73.9567, 40.774],
        [-73.9543, 40.775]
      ]
    },
    "properties": {
      "@feature-type": "arrow",
      "@arrow-scale-zoom": 16,
      "name": "Straight",
      "@arrow-color": "#22433E",
      "@arrow-width": 10,
      "@arrow-rounding": 0
    }
  },
  {
    "type": "Feature",
    "id": "arrow-3",
    "geometry": {
      "type": "LineString",
      "coordinates": [
        [-73.9533, 40.774],
        [-73.9509, 40.775]
      ]
    },
    "properties": {
      "@feature-type": "arrow",
      "@arrow-scale-zoom": 16,
      "name": "Bent the other way",
      "@arrow-color": "#3F80C4",
      "@arrow-width": 4,
      "@arrow-rounding": 1,
      "@arrow-curve": -0.4
    }
  }
]
```

An arrow is drawn as one shape: a shaft `@arrow-width` pixels thick at
`@arrow-scale-zoom`, with a tail and a head at its second position,
sized to match and pointing the way the shaft arrives. `@arrow-rounding`,
from 0 to 1, rounds the head's three points and the tail's corners: at 0
the head is sharp and the tail square, and at 1 they're as round as they go
and the tail is a half circle. It's a share of each corner's room, so an
arrow looks the same at any width. A short arrow's head
and shaft are drawn smaller, so its head is never more than about half its
length. Its shaft is a curve whose middle is `@arrow-curve` of its length
off the straight line between its ends. Arrows are decoration: visitors
can't click them, and they're left out of the legend and the map's feature
list. Removing `@feature-type` makes an arrow a plain line.

## Circles

**Polygons.** A polygon that stays a circle: a Polygon with "@feature-type": "circle" and its center and radius. The polygon is made from them, round on the map, so send null (or any Polygon) as its geometry and the API fills it in. It's styled as an area. Without the two properties below it's a plain area, whose vertices can be edited.

| Property | Type | Default | Description |
| --- | --- | --- | --- |
| `@feature-type` | `"circle"` | None | Makes a polygon a circle, made from the property below. |
| `@circle` | `object` | None | A circle's center and radius. It's drawn round on the map (Web Mercator), so its radius is exact at its center. |
| `@circle.center` | `[number, number]` (items in turn: -180 to 180; -85.051129 to 85.051129) | None | Its center, as [longitude, latitude]. |
| `@circle.radius` | `number` (0.1 to 5000000) | None | In meters, at its center. |

```json
{
  "type": "Feature",
  "id": "circle-area",
  "geometry": null,
  "properties": {
    "name": "A 150 m walk",
    "@feature-type": "circle",
    "@circle": {
      "center": [-73.9555, 40.7745],
      "radius": 150
    },
    "@fill-color": "#F2C14E"
  }
}
```

## Rectangles

**Polygons.** A polygon that stays a rectangle, at any angle: a Polygon with "@feature-type": "rectangle" and its center, size and rotation, and how round its corners are if they aren't square. The polygon is made from them, a rectangle on the map, so send null (or any Polygon) as its geometry and the API fills it in. It's styled as an area. Without the two properties below it's a plain area, whose vertices can be edited.

| Property | Type | Default | Description |
| --- | --- | --- | --- |
| `@feature-type` | `"rectangle"` | None | Makes a polygon a rectangle, made from the property below. |
| `@rectangle` | `object` | None | A rectangle's center, size, turn and corners. It's drawn as a rectangle on the map (Web Mercator), so its size is exact at its center. |
| `@rectangle.center` | `[number, number]` (items in turn: -180 to 180; -85.051129 to 85.051129) | None | Its center, as [longitude, latitude]. |
| `@rectangle.width` | `number` (0.1 to 10000000) | None | In meters, at its center: its top and bottom edges. |
| `@rectangle.height` | `number` (0.1 to 10000000) | None | In meters, at its center: its left and right edges. |
| `@rectangle.rotation` | `number` (-180 to 180) | None | How far it's turned clockwise, in degrees: 0 is upright, with its top edge running east–west. |
| `@rectangle.cornerRadius` | `number` (0 to 5000000) | None | How round its corners are, in meters at its center: 0 or none for square corners. Up to half its shorter side, which makes its short ends round; more is drawn as that. |

```json
[
  {
    "type": "Feature",
    "id": "rectangle",
    "geometry": null,
    "properties": {
      "name": "A lot",
      "@feature-type": "rectangle",
      "@rectangle": {
        "center": [-73.958, 40.7745],
        "width": 320,
        "height": 140,
        "rotation": 29
      }
    }
  },
  {
    "type": "Feature",
    "id": "rounded-rectangle",
    "geometry": null,
    "properties": {
      "name": "A plaza",
      "@feature-type": "rectangle",
      "@rectangle": {
        "center": [-73.953, 40.7745],
        "width": 240,
        "height": 160,
        "rotation": 29,
        "cornerRadius": 40
      }
    }
  }
]
```

The API makes a circle's or rectangle's polygon when it saves the map, so
send its geometry as `null` (or any Polygon). The editor resizes them by
their parameters, so they stay circles and rectangles; removing
`@feature-type` and the parameter makes either a plain polygon, whose vertices
can be edited.

## Images

**Images.** A picture fixed to the map, drawn among lines and polygons in the features' order: a Polygon of its four corners (top left, top right, bottom right, bottom left, then the first again) with "@feature-type": "image". The corners place, size and turn the picture.

| Property | Type | Default | Description |
| --- | --- | --- | --- |
| `@feature-type` | `"image"` | None | Makes a Polygon an image, placed by its corners. |
| `@image` | `FeatureImage` | None | The picture, uploaded in the editor. |

The editor moves an image's corners together, keeping the picture in
proportion. Images are drawn under every point and text, and aren't
interactive on published maps.

## Types

Objects inside feature properties.

### FeatureImage

An image uploaded to squidmaps, kept as uploaded and scaled down.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `id` | `string` (a UUID) | Yes |  |
| `url` | `string` (a URL) | Yes | The folder its scaled-down WebP versions are in, each at {url}/{size}.webp: thumb (256px), small (640px), medium (1280px), large (2560px). |
| `original` | `string` (a URL) | Yes | The file as uploaded. |
| `width` | `integer` (above 0) | Yes | The original's width in pixels, upright. |
| `height` | `integer` (above 0) | Yes | The original's height in pixels, upright. |
| `bytes` | `integer` (at least 0) | Yes | Bytes in storage: the original and its scaled versions together. |
| `alt` | `string` (at most 1000 characters) | No | Alt text: what the image shows, for people who can't see it, read out by screen readers. Missing for an image without one. |

## Value types

Kinds of values that appear in several places.

### Bounds

An area as [west, south, east, north], in degrees. South is below north. West is below east, except across the antimeridian (180°), as in GeoJSON: [170, -20, -170, 10] is the area from 170°E to 170°W. Coordinates have at most six decimal places.

`[number, number, number, number]`: items in turn: -180 to 180; -85.051129 to 85.051129; -180 to 180; -85.051129 to 85.051129

### Color

A color in hex, as "#rrggbb", like "#3F80C4".

`string`: matches /^#[0-9a-fA-F]{6}$/

### Icon

A Font Awesome 6 Free icon as "style/name": "solid/mug-hot", "regular/heart" or "brands/github". The style is solid, regular or brands.

`string`: matches /^(solid|regular|brands)\/[a-z0-9]+(-[a-z0-9]+)*$/

### Emoji

One emoji, like "📍", "🇯🇵" or "👩‍🚒", including flags and sequences joined with zero-width joiners.

`string`
