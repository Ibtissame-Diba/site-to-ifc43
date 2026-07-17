# Site to IFC 4.3 (LAFA)

A [QGIS](https://qgis.org) Processing plugin that exports landscape and biodiversity
GIS layers to a **single, georeferenced IFC 4.3 file** (ISO 16739-1:2024), driven by
the German **LAFA** dictionary (*BIM-Fachmodell Landschaft und Freianlage*,
buildingSMART Deutschland 1.0).

Every object is classified against LAFA: the IFC entity, the bSDD classification URI,
and the Tier-2 property names all come from the bundled `lafa_dict.json` (64 classes).
The result opens in BIMvision and passes the official buildingSMART validation service.

This plugin was developed as part of a master's thesis evaluating whether IFC 4.3 can
hold and communicate biodiversity and connectivity data in a shared BIM workflow.

---

## What it does

- Reads four kinds of QGIS layer (zones/areas, vegetation polygons, species points,
  tree points) and maps each to its LAFA class.
- Rebuilds every feature as the correct IFC 4.3 entity — `IfcSpatialZone` for
  biodiversity zones and species occurrences, `IfcGeographicElement` for vegetation and
  trees — with the LAFA classification and Tier-2 properties attached.
- Preserves the GIS origin of every object (source feature id and coordinates) via
  custom `ePset_Source_*` property sets.
- Writes one georeferenced IFC file with a shared base point, optionally sampling a DGM
  terrain raster so every element gets its true elevation.
- Includes two helper tools that turn a canopy-height raster into tree points.

> **Note on viewers:** most LAFA biodiversity classes map to `IfcSpatialZone`, which is
> visible in **BIMvision** and the **buildingSMART validator**, but **not** in Autodesk
> Revit (Revit does not render `IfcSpatialZone`). Use BIMvision to inspect the output.

---

## Requirements

- **QGIS 3.22 or newer** (developed and tested on the latest QGIS release).
- **IfcOpenShell** in QGIS's Python. This is the one dependency you must install
  yourself — it does not ship with QGIS.

### Installing IfcOpenShell

**Windows (OSGeo4W):** open the *OSGeo4W Shell* (search it in the Start menu) and run:

```
python -m pip install ifcopenshell
```

**macOS / Linux:** run the same command in the Python environment QGIS uses:

```
python3 -m pip install ifcopenshell
```

Then restart QGIS. If IfcOpenShell is missing, the plugin stops with a clear message
telling you this exact command — so if you see that error, this is the fix.

(`numpy` is also used, but it already ships with QGIS.)

---

## Installation

1. Download the latest `site_to_ifc43.zip` from the
   [Releases](../../releases) page.
2. In QGIS: **Plugins → Manage and Install Plugins → Install from ZIP**.
3. Select the downloaded ZIP and click **Install Plugin**.
4. The tools appear in the **Processing Toolbox** under **Open BIM → Site to IFC 4.3 (LAFA)**.

If you don't see them, open **Processing → Toolbox** and make sure the Processing panel
is enabled.

---

## Usage

Open the Processing Toolbox and run **Site to IFC 4.3 (LAFA)**. All layer inputs are
optional — supply whichever you have:

| Input | Maps to | Notes |
|---|---|---|
| **Zone / area polygons** | `IfcSpatialZone` | FFH, LSG, Biotop, LRT, Habitat, Wirkzone … classified by layer name |
| **Vegetation polygons** | `IfcGeographicElement` (Vegetationsflaeche) | |
| **Species points** | `IfcSpatialZone` / OCCUPANCY | split into Fauna / Flora by a *kingdom* field (Animalia / Plantae) |
| **Tree points** | `IfcGeographicElement` (Pflanze) | |

Other parameters:

- **Target CRS** — everything is reprojected to this (default `EPSG:25832`).
- **DGM terrain raster** *(optional)* — gives every element its true Z.
- **Base point E / N / H** — leave at `0` for an automatic shared origin.
- **Name / kingdom fields** *(optional)* — attribute columns to read labels and
  animal/plant split from.
- **LAFA dictionary JSON** *(optional)* — leave blank to use the bundled `lafa_dict.json`.

The output is a single `.ifc` file. Open it in BIMvision to inspect the classified
objects, or upload it to the [buildingSMART validation service](https://validate.buildingsmart.org/).

### Customising the mapping

Three editable blocks near the top of `site_to_ifc43_algorithm.py` let you adapt the
plugin without touching the export logic:

- `LAYER_RULES` — which layer-name tokens map to which LAFA class.
- `SPECIES_KINGDOM` — how kingdom values are read as Fauna vs Flora.
- `FIELD_ALIASES` — alternative attribute-column names to look for.

---

## How it works (short version)

The plugin does not invent a schema. It reads the LAFA dictionary, and for every input
feature it looks up the LAFA class, attaches the dictionary's classification URI and
properties, and builds the matching IFC 4.3 entity with IfcOpenShell. Nothing is carried
in IFC's *native* property sets — everything travels through IFC's **extension
mechanism** (the buildingSMART Data Dictionary plus custom source property sets). That
extension mechanism is the point: it is what lets a landscape-specific vocabulary live
inside a standard the whole construction industry already exchanges.

---

## Citing

If you use this plugin in academic work, please cite the thesis (details to be added).

## License

Released under the MIT License — see [LICENSE](LICENSE). You are free to use, modify, and
build on it, including in commercial work; please keep the copyright notice.

## Author

Ibtissame Diba — International Master of Landscape Architecture (IMLA),
HfWU Nürtingen-Geislingen / HSWT, 2026.  
Contact: ibtissamediba@hotmail.com
