# Site to IFC 4.3 (LAFA) — v3.1.0

A QGIS Processing plugin that exports landscape and biodiversity GIS layers to a single,
georeferenced **IFC 4.3** file (ISO 16739-1:2024), driven by the German **LAFA**
dictionary (*BIM-Fachmodell Landschaft und Freianlage*, buildingSMART Deutschland 1.0).

Developed as part of a master's thesis evaluating whether IFC 4.3 can hold and communicate
biodiversity and connectivity data inside a shared BIM workflow.

## What's in this release

- **Dictionary-driven export** — every object is classified against the bundled
  `lafa_dict.json` (64 LAFA classes). The IFC entity, the bSDD classification URI, and the
  Tier-2 property names all come from the dictionary, not from hardcoded logic.
- **Four input layer types** — zones/areas, vegetation polygons, species points, and tree
  points, each mapped to the correct IFC 4.3 entity (`IfcSpatialZone` for biodiversity
  zones and species occurrences, `IfcGeographicElement` for vegetation and trees).
- **GIS origin preserved** — source feature id and coordinates travel into custom
  `ePset_Source_*` property sets, so every object keeps its lineage.
- **One georeferenced file** — shared base point, optional DGM terrain sampling for true
  elevation, reprojected to a target CRS (default EPSG:25832).
- **Two canopy helper tools** — turn a canopy-height raster into tree points.
- Output opens in **BIMvision** and passes the official **buildingSMART validation
  service** (STEP syntax, schema, normative rules, industry practices).

## Requirements

- QGIS 3.22 or newer (developed on the latest QGIS release).
- **IfcOpenShell** in QGIS's Python — install with `python -m pip install ifcopenshell`
  from the OSGeo4W Shell (Windows) or your QGIS Python environment (macOS/Linux), then
  restart QGIS. If it's missing, the plugin stops with a clear message giving this exact
  command. (`numpy` already ships with QGIS.)

## Install

Download `site_to_ifc43.zip` below, then in QGIS:
**Plugins → Manage and Install Plugins → Install from ZIP** → select the file → Install.
The tools appear in the Processing Toolbox under **Open BIM → Site to IFC 4.3 (LAFA)**.

See the [README](README.md) for full usage and the editable mapping blocks.

## Note on viewers

Most LAFA biodiversity classes map to `IfcSpatialZone`, which renders in BIMvision and the
buildingSMART validator but **not** in Autodesk Revit. Use BIMvision to inspect the output.

## License

MIT — free to use, modify, and build on, including commercially; keep the copyright notice.
