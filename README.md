# hika-data

The data of [HIKA](https://github.com/SosiNone/hika), a sci-fi HUD for exploring Swiss hiking
routes: the **atlas**, a national tiled store of terrain, hiking trails, places and routes, built
from Swiss open government data. GitHub Pages serves it at
[sosinone.github.io/hika-data/v4/](https://sosinone.github.io/hika-data/v4/manifest.json), where
the app at [sosinone.github.io/hika](https://sosinone.github.io/hika/) reads it.

This repository holds only built files. The pipeline that builds and checks them, and the
description of the format, are in the HIKA repository (`scripts/build-atlas.ts`,
`scripts/check-atlas.ts` and the "Atlas format" section of `AGENTS.md`).

## Sources and terms

The files are processed data, published by HIKA under its own name as the FSDI terms ask. They are
not covered by HIKA's MIT licence: each source keeps its owner's terms. If you reuse them, credit
the sources as below.

| Data | Owner | Dataset | Terms |
|---|---|---|---|
| swissALTI3D (terrain) | ©swisstopo | [swissALTI3D](https://www.swisstopo.admin.ch/en/height-model-swissalti3d) | [swisstopo OGD](https://www.swisstopo.admin.ch/en/terms-of-use-free-geodata-and-geoservices): any use, with the source credited |
| swissTLM3D Wanderwege (trails) | ©swisstopo | [swissTLM3D](https://www.swisstopo.admin.ch/en/landscape-model-swisstlm3d) | swisstopo OGD |
| swissNAMES3D (places, landscape and valley outlines) | ©swisstopo | [swissNAMES3D](https://www.swisstopo.admin.ch/en/landscape-model-swissnames3d) | swisstopo OGD |
| swissBOUNDARIES3D (borders) | ©swisstopo | [swissBOUNDARIES3D](https://www.swisstopo.admin.ch/en/landscape-model-swissboundaries3d) | swisstopo OGD |
| Wanderland Schweiz (SchweizMobil hiking routes) | Federal Roads Office FEDRO, SchweizMobil and the cantons | [ch.astra.wanderland](https://data.geo.admin.ch/browser/index.html#/collections/ch.astra.wanderland) | [opendata.swiss, open use, must provide the source](https://opendata.swiss/en/terms-of-use/#terms_by) |
| Route altitudes | ©swisstopo, via the geo.admin.ch profile service (swissALTI3D DTM2) | [Profile service](https://api3.geo.admin.ch/services/sdiservices.html#profile) | [FSDI general terms](https://www.geo.admin.ch/en/general-terms-of-use-fsdi), within its fair-use limits |

`v4/manifest.json` lists the same sources with their links and terms.

HIKA is not affiliated with or endorsed by swisstopo, FEDRO or SchweizMobil. The data is processed
(resampled, tiled, routed and timed) and comes **without any warranty**. It is for exploring
hikes, not for navigation: check the signposts, the conditions and any closures before you go.

## Layout

```
v4/
  manifest.json      format, tile grid, the tiles present, the reliefs' cell, sources, build time
  catalogue.json     every route: meta, stats, bbox, sparkline, the areas that list it
  regions.json       the 10 mountain ranges and their areas, with facts and shared border arcs
  locator.json       national border and cantons; switzerland.u16 is the coarse relief,
                     switzerland-1km.u16 the 1 km relief for the zoomed map
  terrain/<E>_<N>.bin   5 km tiles of 20 m cells (key = south-west corner in km)
  trails/<E>_<N>.bin    the signposted trails, each line whole in the tile of its centre
  places/<E>_<N>.json   swissNAMES3D places
  routes/<id>.bin       one whole route each
  relief/<id>.bin       each area whole at 100 m (its extent plus 4 km), averaged from the
                        terrain tiles, for the 3D view around a route's window
```

A new format goes into a new directory (`v5/`) next to the current one, until the app release
that reads it is live, and the old one is removed once that release is live.

## Updating

From a checkout of HIKA next to this one:

```bash
task atlas ATLAS=../hika-data/v4
```

That builds the atlas into `v4/` and checks it. A rebuild writes only the files whose bytes
changed, so `git status` shows what the update touched. Commit the changes and push them, or open
a pull request, which runs `check:atlas` from HIKA. A push to `main` publishes the atlas.
