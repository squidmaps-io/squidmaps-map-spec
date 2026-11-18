# Map

_Last updated November 16, 2026_

> A map is one JSON object, holding its own fields and its features.

> **Beta:** squidmaps is in beta. Features, the map spec and these docs may change without notice, and things may occasionally break. Export a copy of any map that matters to you, and tell us when something isn't right: use Beta feedback, or email support@squidmaps.io.

## Fields

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `name` | `string` (1 to 100 characters) | Yes | Shown at the top of the map's page and in link previews. Its page's address is made from it. |
| `description` | `string` (at most 100000 characters) | Yes | Markdown, shown under the name in the panel over the published map. HTML is escaped. |
| `basemap` | `Basemap or null` | Yes | The map under the features. Null shows none. |
| `extent` | `Extent` | Yes | Where the published map opens, and how far visitors can zoom and pan it. |
| `view` | `View or null` | Yes | Where the published map opens, unless its extent has initialBounds. Null until the map is first viewed in the editor; it then fits the features. |
| `legend` | `Legend or null` | Yes | A panel in the corner explaining the map's symbols. Null shows none. |
| `panel` | `Panel` | No, `{"visible":true,"featureList":false,"search":false}` | The panel over the published map, with its name and description: whether it's shown, and whether it lists and searches the features. Left out, it's shown, without either. |
| `controls` | `Controls` | No, `{"zoom":true,"fullscreen":false}` | Buttons over the published map, top right: zoom buttons and a fullscreen button. Left out, it has zoom buttons and no fullscreen button. |
| `features` | `array of Feature` | Yes | The map's features, top of the list first. Points (and text) are drawn above lines, areas and images; within each, features earlier in the list are drawn on top. |

## Types

The objects inside a map. Where a type has several forms, one field (like
`type`) says which.

### Basemap

The map under the features, chosen by its type.

#### `type: "SQUIDMAPS"`

One of squidmaps' own basemaps, by name.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `type` | `"SQUIDMAPS"` | Yes |  |
| `style` | `"streets" \| "light" \| "gray" \| "dark"` | Yes | Which of squidmaps' basemaps. |
| `opacity` | `number` (0 to 1) | No, `1` | From 0 (invisible) to 1 (opaque). |
| `labelVisibility` | `boolean` | No, `true` | Whether the basemap's own labels are shown. |

#### `type: "CUSTOM"`

A custom basemap: one of squidmaps' basemaps with its colors and layers changed, saved to someone's account. Changes to it reach the maps that use it.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `type` | `"CUSTOM"` | Yes |  |
| `id` | `string` (a UUID) | Yes | The custom basemap's id. |
| `opacity` | `number` (0 to 1) | No, `1` | From 0 (invisible) to 1 (opaque). |
| `labelVisibility` | `boolean` | No, `true` | Whether the basemap's own labels are shown. |

#### `type: "STYLE"`

A MapLibre style, by its style JSON's URL.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `type` | `"STYLE"` | Yes |  |
| `name` | `string` (1 to 100 characters) | Yes | What the editor calls the basemap. |
| `url` | `string` (a URL) | Yes | Where the basemap is served. |
| `opacity` | `number` (0 to 1) | No, `1` | From 0 (invisible) to 1 (opaque). |
| `provider` | `string` | No | Who provides the style, for credit. |

#### `type: "XYZ"`

Raster tiles, by a URL template with {z}, {x} and {y} in it.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `type` | `"XYZ"` | Yes |  |
| `name` | `string` (1 to 100 characters) | Yes | What the editor calls the basemap. |
| `url` | `string` (a URL) | Yes | Where the basemap is served. |
| `opacity` | `number` (0 to 1) | No, `1` | From 0 (invisible) to 1 (opaque). |
| `token` | `string` | No | An access token for the tiles, if they need one. |
| `tms` | `boolean` | No, `false` | Whether tile rows count up from the south (TMS). |

#### `type: "TILEJSON"`

Raster tiles described by a TileJSON URL.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `type` | `"TILEJSON"` | Yes |  |
| `name` | `string` (1 to 100 characters) | Yes | What the editor calls the basemap. |
| `url` | `string` (a URL) | Yes | Where the basemap is served. |
| `opacity` | `number` (0 to 1) | No, `1` | From 0 (invisible) to 1 (opaque). |
| `token` | `string` | No | An access token for the tiles, if they need one. |

### Extent

Where the published map opens, and how far visitors can zoom and pan it.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `zoom` | `ZoomLimit` | Yes | The zoom levels the published map can be viewed at. |
| `bounds` | `Bounds or null` | No, `null` | An area visitors can't pan the middle of the map out of, so anything in it can be brought to the middle of the screen, and they can't zoom out past where it fills the screen. Null lets them pan anywhere. |
| `initialBounds` | `Bounds or null` | No, `null` | An area the published map opens with fully in view. Null opens it at the saved view. |
| `scrollZoom` | `boolean` | No, `true` | Whether scrolling over the published map zooms it. Off, visitors hold Ctrl or ⌘ while scrolling to zoom, and a notice says so, so a map embedded in a page doesn't catch the page's scrolling. In an embed, touch screens also move the map with two fingers. |

### ZoomLimit

The zoom levels the published map can be viewed at.

#### `mode: "range"`

Visitors zoom between two levels.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `mode` | `"range"` | Yes |  |
| `min` | `number` (0 to 22) | Yes | The farthest out. Below max. |
| `max` | `number` (0 to 22) | Yes | The farthest in. |

#### `mode: "fixed"`

Visitors see one zoom level, and can't zoom.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `mode` | `"fixed"` | Yes |  |
| `level` | `number` (0 to 22) | Yes | The only zoom level. |

### View

Where the map is looking. The editor saves where it was left, and the published map opens there.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `center` | `[number, number]` (items in turn: -180 to 180; -90 to 90) | Yes | [longitude, latitude], in degrees. |
| `zoom` | `number` (0 to 22) | Yes | A zoom level, as in MapLibre. |

### Legend

A panel in the bottom right corner of the published map, with a row for each symbol worth explaining.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `title` | `string` (at most 100 characters) | Yes | Shown above the rows. Empty for none. |
| `rows` | `array of LegendRow` (1 to 50 items) | Yes | In display order. |

### LegendRow

A symbol and its label.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `label` | `string` (at most 100 characters) | Yes | Shown beside the symbol. |
| `symbol` | `LegendSymbol` | Yes | How a row's symbol is drawn: a copy of a feature's style, with the same fields as the feature properties for its kind. It stays as drawn when features change. |

### LegendSymbol

How a row's symbol is drawn: a copy of a feature's style, with the same fields as the feature properties for its kind. It stays as drawn when features change.

#### `type: "circle"`

A circle point's style.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `type` | `"circle"` | Yes |  |
| `fill` | `Color` | Yes | The circle's color. |
| `radius` | `number` (2 to 40) | Yes | In pixels, not counting the stroke. |
| `stroke` | `Color` | Yes | The ring around the circle. |
| `strokeWidth` | `number` (0 to 10) | Yes | The ring's width in pixels, outside the radius. |
| `icon` | `Icon or null` | Yes | A Font Awesome icon in the circle. |
| `iconColor` | `Color or null` | Yes | The icon's color. Without one, white or dark ink, whichever contrasts the fill. |

#### `type: "emoji"`

An emoji point's style.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `type` | `"emoji"` | Yes |  |
| `emoji` | `Emoji` | Yes | One emoji, drawn as its Twemoji image. |
| `size` | `number` (12 to 64) | Yes | The emoji's width and height in pixels. |

#### `type: "marker"`

A marker pin's style.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `type` | `"marker"` | Yes |  |
| `shell` | `Color` | Yes | The pin's outer color. |
| `disc` | `Color` | Yes | The disc in the pin's head. |
| `icon` | `Icon or null` | Yes | A Font Awesome icon on the disc. Without one, it's empty. |
| `iconColor` | `Color or null` | Yes | The icon's color. Without one, white or dark ink, whichever contrasts the disc. |
| `size` | `number \| "small" \| "medium" \| "large"` | No | The pin's width in pixels, 16 to 96; it's 1.14 times as tall. Or a preset: small, medium or large. |

#### `type: "custom"`

A custom icon's style.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `type` | `"custom"` | Yes |  |
| `icon` | `CustomIcon or null` | Yes | The picture, from the account's custom icons. Without one, a placeholder. |
| `shape` | `"round" \| "original"` | Yes | round: cropped to a circle from its center, on the point, with a stroke. original: uncropped with any transparency, standing on the point with its bottom center there, like a pin. |
| `size` | `number \| "small" \| "medium" \| "large"` | Yes | Its size in pixels, 16 to 96: a round icon's diameter, inside its stroke, or the height of one drawn as is. Or a preset: small, medium or large. |
| `stroke` | `Color` | Yes | The ring around a round icon. |
| `strokeWidth` | `number` (0 to 10) | Yes | The ring's width in pixels, outside the picture. |

#### `type: "line"`

A line's style.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `type` | `"line"` | Yes |  |
| `color` | `Color` | Yes | The line's color. |
| `width` | `number` (0.5 to 20) | Yes | In pixels. |
| `opacity` | `number` (0 to 1) | Yes | From 0 (invisible) to 1 (opaque). |
| `dasharray` | `array of number` (at most 8 items, each 0 to 100) | Yes | Dash and gap lengths in turn, in line widths, like [4, 2]. Empty is solid. |
| `cap` | `"butt" \| "round" \| "square"` | Yes | How the line's ends are drawn. |

#### `type: "area"`

An area's style.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `type` | `"area"` | Yes |  |
| `color` | `Color` | Yes | The line's color. |
| `width` | `number` (0.5 to 20) | Yes | In pixels. |
| `opacity` | `number` (0 to 1) | Yes | From 0 (invisible) to 1 (opaque). |
| `dasharray` | `array of number` (at most 8 items, each 0 to 100) | Yes | Dash and gap lengths in turn, in line widths, like [4, 2]. Empty is solid. |
| `cap` | `"butt" \| "round" \| "square"` | Yes | How the line's ends are drawn. |
| `fillColor` | `Color` | Yes | The area's inside color. |
| `fillOpacity` | `number` (0 to 1) | Yes | From 0 (no fill) to 1 (opaque). |

### CustomIcon

A picture from an account's custom icons, uploaded in the editor.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `id` | `string` (a UUID) | Yes |  |
| `url` | `string` (a URL) | Yes | Its WebP file. |
| `width` | `integer` (above 0) | Yes | The file's width in pixels. |
| `height` | `integer` (above 0) | Yes | The file's height in pixels. |

### Panel

The panel over the published map: whether it's shown, and what it offers visitors besides the map's name and description.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `visible` | `boolean` | No, `true` | Whether the published map has the panel at all. Off, visitors see just the map, and its legend in the corner. |
| `featureList` | `boolean` | No, `false` | A list of the map's features by folder, with a checkbox to hide each folder. |
| `search` | `boolean` | No, `false` | A box to find features by name or description. |

### Controls

Buttons over the published map, top right: zoom buttons, on by default, and a fullscreen button, off by default.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `zoom` | `boolean` | No, `true` | Buttons to zoom in and out. Visitors can still zoom by scrolling, pinching or double-clicking, as the extent allows. A fixed zoom has none. |
| `fullscreen` | `boolean` | No, `false` | A button that fills the screen with the map, and back. In an embed, the iframe needs allowfullscreen, as squidmaps' embed code has. |

### Feature

A GeoJSON Feature with a required string id. Other members, like bbox, are kept.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `type` | `"Feature"` | Yes |  |
| `id` | `string` (not empty) | Yes | Unique within the map. |
| `geometry` | `Geometry or null` | Yes | Where the feature is. Null for a feature without one. |
| `properties` | `object` | Yes | Everything about the feature: its data, and how it looks and behaves, under "@"-prefixed keys. See Feature properties. |

### Geometry

A GeoJSON geometry (RFC 7946): Point, MultiPoint, LineString, MultiLineString, Polygon, MultiPolygon or GeometryCollection. Coordinates are [longitude, latitude] with at most six decimal places.
