# SVG Map Generator (Python)

Generate clean, lightweight **SVG maps** from GeoJSON geometry. The code parses common geometry types, builds SVG path data, and writes a styled SVG, optionally using an SVG template for canvas/background.

## What’s inside

- Core geometry models for **Point**, **LineString**, **Polygon**, **MultiPolygon**, and a **Composite** wrapper.
- **GeoJSON parsing** that converts coordinate arrays into the internal geometry objects.
- **Bounding-box** utilities to compute per-geometry and global extents.
- A simple **fit transform** (scale + translate) to map data space to the SVG viewport.
- **Path builders** that emit `<path>` / `<circle>` elements with minimal styling.
- Optional **SVG template** support (`map-template.svg`) to keep size, viewBox, and shared styles consistent.
- Example data to demonstrate city/region outlines.

## How it works

1. **Parse input**  
   GeoJSON Feature/Geometry objects are inspected by `type` and converted into in-memory classes:
   - `Point` → single coordinate  
   - `LineString` → ordered list of points  
   - `Polygon` → outer ring (+ optional holes)  
   - `MultiPolygon` → multiple polygons  
   - `Composite` → groups heterogeneous shapes

2. **Normalize geometry**  
   Rings/lines become ordered point arrays. Polygon holes are tracked as separate subpaths.

3. **Compute extents**  
   Each geometry exposes `bounding_box()`. A global `(xmin, ymin, xmax, ymax)` defines the data extent.

4. **Viewport mapping**  
   Using the template `viewBox` (or a default canvas), a **scale + translate** is calculated to fit the data to the viewport with aspect ratio preserved and a small padding.

5. **Build SVG elements**  
   - `Point` → `<circle cx="" cy="" r="..."/>`  
   - `LineString` → `<path d="M x y L x y …"/>`  
   - `Polygon/MultiPolygon` → `<path d="M … Z … Z"/>` (outer ring then holes)  
   Styling (fill, stroke, opacity) is applied inline or inherited from a `<style>` block.

6. **Template merge / output**  
   If `map-template.svg` exists, generated shapes are injected into its root; otherwise a minimal SVG root is created. The final SVG is written to disk—resolution-independent and ready for the web or vector editors.

## Notes & assumptions

- Coordinates are treated as planar **lon/lat** for simplicity (good for city/region scale).  
- Polygon holes are supported via multi-subpath winding.  
- Visual design is minimal by default; prefer the template for consistent theming.

## Potential extensions

- True cartographic projections (Mercator, Lambert, etc.).  
- Style rules from GeoJSON feature properties.  
- Label placement and symbol layers.  
- CLI options for size, padding, and style presets.
