# Replication materials

This public package accompanies “Lost in Aggregation: Quantifying Measurement Error from Geographic Centroids” by Ali Furkan Kalay and Henry Wen, accepted for publication in *Economic Record*. It contains the analysis code, saved aggregate outputs, software information, data-source instructions, and checksums for the files used by the authors.

The public repository is [https://github.com/alfurka/quantify-measurement-error-replication-codes](https://github.com/alfurka/quantify-measurement-error-replication-codes).

The workflow begins with prepared inputs. It does not automate every original download and upstream cleaning step.

## Support and updates

The journal supplement records the replication materials supplied for publication. The GitHub repository is the maintained version and may be updated if a problem is found or if the replication instructions can be made clearer. Corrections and material changes will be documented through the repository's commit history and releases.

Researchers are welcome to open a GitHub issue with questions about replication or data access. Where the applicable licences and terms permit particular data files to be shared, the authors will arrange the transfer privately rather than through the public repository. Where redistribution is not permitted, the authors will help researchers reconstruct the required files from the documented sources. If a GitHub issue does not receive a response, for example because its notification was missed, please also email the authors using the correspondence details in the article and include a link to the issue.

## Contents

```text
Replication/
├── Code/
│   ├── Data Preparation.Rmd
│   └── Regressions_v10.Rmd
├── Outputs/
│   ├── data/
│   ├── figures/
│   └── tables/
├── README.md
└── package_versions.txt
```

`Data Preparation.Rmd` constructs the analysis GeoPackage from the prepared source files. `Regressions_v10.Rmd` performs the analysis and produces the saved aggregate outputs. R and package versions are recorded in [`package_versions.txt`](package_versions.txt).

## Reproducing the analysis

Create the following local directories beside `Code` and `Outputs`:

```text
Data/
├── Inputs/
│   ├── Amenities/
│   ├── Boundaries/
│   ├── GNAF/
│   └── Zoning/
├── Intermediate/
└── Analysis/
```

Place the required files at the paths listed under **Data sources** and **Reference input checksums** below. Then use `Code` as the working directory:

1. Run `Data Preparation.Rmd`. It writes working files to `Data/Intermediate` and creates `Data/Analysis/transactions_with_GNAF_and_amenities_2022_2025_nsw_updated_20260726.gpkg`.
2. Run `Regressions_v10.Rmd`. It reads that GeoPackage and regenerates the contents of `Outputs`.

A researcher who already has the final analysis GeoPackage can run `Regressions_v10.Rmd` without rerunning data preparation. The regression notebook can also resolve its paths when launched from the replication root.

## Saved outputs and validation

The supplied `Outputs` directory contains aggregate CSV results, publication tables, and figures from the verified analysis. The identifier-level ambiguity file and serialized R results bundle are excluded. The large transaction-map PDF and duplicate transaction-map PNG are also omitted; the publication PNG is retained as `Outputs/figures/map_300dpi.png`, and the notebook can regenerate all map formats.

`Outputs/manifest.csv` records the size and MD5 checksum of every supplied output file.

Following assembly of the private verification folder, `Regressions_v10.Rmd` was run in an isolated copy. It completed successfully, all validation checks passed, and the regenerated estimates, diagnostics, tables, and figures matched the saved reference results. `Data Preparation.Rmd` was checked for complete paths and required inputs but was not rerun during package assembly.

Expected validation:

- 340,856 observations: 281,621 houses and 59,235 apartments;
- 126 paired-result rows;
- 36 FWL rows with the identity check passing;
- 36 corrected-centroid rows;
- 42 diagnostic rows; and
- 180 subsample rows, including nine non-estimable rows.

The bootstrap uses 999 repetitions and seed 20260714. No referenced R package was missing when the package was assembled.

## Data sources

| Required input | Source and version | Preparation record |
|---|---|---|
| `Data/Inputs/nsw-property-sales-data-updated20250630.csv` | NSW Valuer General [Bulk Property Sales Information](https://www.valuergeneral.nsw.gov.au/design/bulk_psi_content/bulk_psi), cleaned through [NSW Property Sales Data](https://nswpropertysalesdata.com/) using the public [NSW property sales data cleaner](https://github.com/jameselks/nsw-property-sales-data-cleaner). Local file dated 30 June 2025. | The public source provides a moving current release. A current download can be restricted to January 2022 through June 2025, but later corrections may prevent an exact byte-for-byte reconstruction. The checksum below identifies the study input used by the authors. |
| Four PSV files in `Data/Inputs/GNAF` | [G-NAF previous versions](https://data.gov.au/data/dataset/geoscape-geocoded-national-address-file-g-naf-previous-versions), August 2024, GDA2020, NSW. | The workflow uses only the address detail, default geocode, street locality, and locality tables listed below. The original archive filename and download date were not recorded. |
| `sa1_all.gpkg`, `sa2_all.gpkg`, `poa_all.gpkg`, and the retained GCCSA and state shapefiles | ABS [ASGS Edition 3 digital boundary files](https://www.abs.gov.au/statistics/standards/australian-statistical-geography-standard-asgs/edition-3-july-2021-june-2026/access-and-downloads/digital-boundary-files), 2021 GDA2020. | The three GeoPackages are prepared copies of the SA1, SA2, and Postal Area products. The GCCSA boundary identifies Greater Sydney, and the state boundary is used for the transaction map. |
| `Data/Inputs/seifa.xlsx` | ABS [SEIFA 2021](https://www.abs.gov.au/statistics/people/people-and-communities/socio-economic-indexes-areas-seifa-australia/latest-release), SA1 data cube released 27 April 2023; manuscript access date 4 November 2025. | The required columns were retained and named `SA1`, `irsd`, `irsad`, `ier`, `ieo`, and `population`. |
| Three GeoPackages in `Data/Inputs/Amenities` | © OpenStreetMap contributors, from the [Geofabrik Australia](https://download.geofabrik.de/australia-oceania/australia.html) snapshot `australia-latest-30july2024.osm.pbf`, dated 30 July 2024. | Extracted on 14 September 2024 with Osmium Tool using an NSW boundary derived from ABS ASGS Edition 3. The prepared layers contain health services, parks, and public-transport stops. |
| `Data/Inputs/Zoning/EPI_Land_Zoning.*` | NSW Department of Planning, Housing and Infrastructure [EPI Land Zoning](https://www.planningportal.nsw.gov.au/opendata/dataset/environment-planning-instrument-local-environmental-plan-land-zoning), February 2026 layer accessed 23 February 2026. | The workflow uses residential zones R1--R5 to calculate residential coverage. |
| `Data/Inputs/hopskell_all_mc.rds` | Precomputed Hopkins--Skellam results for the SA1, SA2, and Postal Area boundaries used in the study. | The notebook joins these results rather than rerunning the simulations. The original random-number seed was not recorded, so the retained file is the reference input. |
| `Data/Inputs/transaction_map_reference.png` | Previously generated manuscript transaction map. | Used only if neither transaction GeoPackage provides point geometry. |

The intermediate transaction file is `Data/Intermediate/transactions_with_GNAF_and_amenities_2022_2025_revision.gpkg`. The final regression input is `Data/Analysis/transactions_with_GNAF_and_amenities_2022_2025_nsw_updated_20260726.gpkg`.

### OpenStreetMap extraction details

The Australia PBF was clipped with `nsw_boundary.geojson`, created from the ABS ASGS Edition 3 NSW boundary. The example commands retained from the extraction record are:

```text
osmium extract -p nsw_boundary.geojson australia-latest-30july2024.osm.pbf -o nsw-30july2024.osm
osmium tags-filter nsw-30july2024.osm w/leisure=park -o nsw-parks.osm.pbf
```

The filters were:

- parks: `leisure=park`;
- health: `amenity=clinic`, `amenity=doctors`, and `amenity=hospital`; and
- public transport: `public_transport=station`, `public_transport=platform`, `public_transport=stop_position`, `railway=tram_stop`, `highway=bus_stop`, and `highway=platform`.

The complete literal conversion commands were not retained. The three amenity GeoPackages and their checksums below identify the exact prepared inputs. They declare EPSG:4326; the historical notebook assigns EPSG:7844 before spatial calculations. That behaviour is preserved in the replication code.

## Reference input checksums

The following SHA-256 values identify the exact input and constructed files used for the authors' verification run. These files are not included in this public package.

| Role | Dataset | File | SHA-256 |
|---|---|---|---|
| analysis | Final analysis dataset | `Data/Analysis/transactions_with_GNAF_and_amenities_2022_2025_nsw_updated_20260726.gpkg` | `24b50e8061f710074dcf8673682acada1eb1a15e074ad4c059dafaa1937b34b7` |
| input | OpenStreetMap NSW amenities | `Data/Inputs/Amenities/nsw_hospital.gpkg` | `f8b87268d9a49c112a7cc2896a35ee887894756104ad4b4164bab361beb18b1e` |
| input | OpenStreetMap NSW amenities | `Data/Inputs/Amenities/nsw_park.gpkg` | `c9fd81fd33c80e75d7beaf31bc53a4c64e5ef578073f337eb02173f99a3c5a41` |
| input | OpenStreetMap NSW amenities | `Data/Inputs/Amenities/nsw_railstop.gpkg` | `ddb6d90eec27b43c94f14d291ed411fe82890bf7be4f6332d35ae2d304bbd403` |
| input | ABS ASGS 2021 | `Data/Inputs/Boundaries/GCCSA_2021_AUST_GDA2020.dbf` | `7b2aa6cf304e6907353f8418b502e993939522692c1849b2551afc08441a960b` |
| input | ABS ASGS 2021 | `Data/Inputs/Boundaries/GCCSA_2021_AUST_GDA2020.prj` | `394fe7500c52df3a75d6832b9c61aae1b4391b34dbc71293674dd2219912321f` |
| input | ABS ASGS 2021 | `Data/Inputs/Boundaries/GCCSA_2021_AUST_GDA2020.shp` | `53f4e0e9295414eb65a6b9c92988b23eab0d411247349eea61f33c1ed9a0c810` |
| input | ABS ASGS 2021 | `Data/Inputs/Boundaries/GCCSA_2021_AUST_GDA2020.shx` | `78b8457acf9598656910f8a3735ab6b08a2936668395de145a6454eeb5e44777` |
| input | ABS ASGS 2021 | `Data/Inputs/Boundaries/STE_2021_AUST_GDA2020.dbf` | `ce6817a7d2ef4c109a8aa03d1197e648ae86fcba77085785086137b262022e2a` |
| input | ABS ASGS 2021 | `Data/Inputs/Boundaries/STE_2021_AUST_GDA2020.prj` | `394fe7500c52df3a75d6832b9c61aae1b4391b34dbc71293674dd2219912321f` |
| input | ABS ASGS 2021 | `Data/Inputs/Boundaries/STE_2021_AUST_GDA2020.shp` | `41cfe58aba86a8078e8ec2aafdf70f8558b9691da6d3ad62d1ae317a002ccc44` |
| input | ABS ASGS 2021 | `Data/Inputs/Boundaries/STE_2021_AUST_GDA2020.shx` | `94942abe7cc6b55a3eccd371e1d37108697c968b249949946ec542759b17aa48` |
| input | ABS ASGS 2021 | `Data/Inputs/Boundaries/poa_all.gpkg` | `b7d7756adde983373ad7618f8d378e887b186ceff5dd95ccc119c008216b47d5` |
| input | ABS ASGS 2021 | `Data/Inputs/Boundaries/sa1_all.gpkg` | `3f11b76e78274010b7d7d32e5beb3380f2a40def5cd4c063d0bac0a16dbe7d7b` |
| input | ABS ASGS 2021 | `Data/Inputs/Boundaries/sa2_all.gpkg` | `6a7406c2dc4961ee517bca7fe9b8454cb304ba1a2601bc7dc5576aa4180d356b` |
| input | G-NAF August 2024 NSW | `Data/Inputs/GNAF/NSW_ADDRESS_DEFAULT_GEOCODE_psv.psv` | `a21625928b79f867e5f5f752142a748e454a02146ce96e253b87044fa6a0285b` |
| input | G-NAF August 2024 NSW | `Data/Inputs/GNAF/NSW_ADDRESS_DETAIL_psv.psv` | `07b3247689f06e08887379bc7ffbae9149fee048bb40491e5fe3bc788a34f74e` |
| input | G-NAF August 2024 NSW | `Data/Inputs/GNAF/NSW_LOCALITY_psv.psv` | `6b756824ac065105198637e35f2511303b4458e55f68a7c23fec6eaafa726736` |
| input | G-NAF August 2024 NSW | `Data/Inputs/GNAF/NSW_STREET_LOCALITY_psv.psv` | `5337e3246dcad18ce1d50fdb3de83dc4c1753bb1c14582180111879d79f6268a` |
| input | NSW EPI Land Zoning | `Data/Inputs/Zoning/EPI_Land_Zoning.dbf` | `2a628147f5c8c5b9689131d9fce4851bfbf45217effda55fe69716ede32790c0` |
| input | NSW EPI Land Zoning | `Data/Inputs/Zoning/EPI_Land_Zoning.prj` | `081a33facdd6081af2a0de0ca77f03d878fe3d06741c22881be8220462361cb4` |
| input | NSW EPI Land Zoning | `Data/Inputs/Zoning/EPI_Land_Zoning.shp` | `6b3779ebbb834676aed439e6d088f5a3c2927149c2fe5cc3cab1f582445c9566` |
| input | NSW EPI Land Zoning | `Data/Inputs/Zoning/EPI_Land_Zoning.shx` | `c745c381b59aa71d108d3ede6268716261b400ffe57a34d64b026f4759eca072` |
| input | Hopkins-Skellam results | `Data/Inputs/hopskell_all_mc.rds` | `6c31e2232706a13dc1891e30de8e95d2c3671b330d420897dbf04a6cc1066927` |
| input | NSW property sales | `Data/Inputs/nsw-property-sales-data-updated20250630.csv` | `1a2a05866d1d49b4b29f3acb66194e72763aa0510368c9cd964b2ef4768089ab` |
| input | ABS SEIFA 2021 | `Data/Inputs/seifa.xlsx` | `025736cac4ac169620defac7ee3ab4aef1eb664c46a7d9fb944a0a0dd0af7275` |
| input | Transaction map fallback | `Data/Inputs/transaction_map_reference.png` | `f8f5de686bd09d924d41c6c1bd1b580e04d0815dd7aed4fa13950f49a92e50b9` |
| intermediate | Prepared transactions | `Data/Intermediate/transactions_with_GNAF_and_amenities_2022_2025_revision.gpkg` | `418978c5ce771acb064271b578a0e1f4d4fbea8708bd4f4828e2726b8fae652f` |

## Data availability and reuse

The data files are omitted because of file size and source-specific licensing. Researchers may contact the authors for cleaned or intermediate files where the applicable terms permit sharing, or for help reconstructing them from the sources and versions above.

NSW Property Sales Information is distributed under CC BY-NC-ND 4.0, so the cleaned and linked transaction files require any permission or PSI licence specified by Value NSW. G-NAF is subject to the Open G-NAF EULA, ABS products to CC BY 4.0, OpenStreetMap-derived data to ODbL 1.0, and NSW zoning to Creative Commons Attribution.
