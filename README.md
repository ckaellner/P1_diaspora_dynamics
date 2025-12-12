# Analysing arrival/exit dynamics of migrants in and to Austria
Carsten Källner - 2024-10-22

This repository contains an R Markdown workflow to analyse arrival, exit, and (optionally) internal movement dynamics of migrants in Austria using administrative residence spell data. It connects to a PostgreSQL data warehouse, builds daily flow matrices (arrivals/exits), fits simple Poisson-based flow parameters (Skellam-style net dynamics), and produces a set of plots and bivariate maps for exploratory and descriptive analysis.

ATTENTION: The data used in this project is subject to strict privacy restrictions set by the Austrian Ministry of the Interior, and access requires an individual data-use agreement to be arranged directly with the Ministry.

## What this does

Loads and prepares data from a PostgreSQL warehouse (person_ranges, first_main_residence, person_status_ranges)

Builds helper structures:

- full daily date sequences

- municipality metadata (population, typologies, urbanisation, NUTS3)

- Austria municipality geometries (simplified shapefile)

- Generates daily matrices of arrivals / exits by chosen grouping (e.g., citizenship, age, municipality, state-urbanisation)

- Estimates Poisson flow parameters and derives net migration (Skellam-style difference between arrival and exit intensities)

- Implements a diaspora-based flow model (pull/push rates scaled by diaspora size) including half-life estimates

- Produces plots and maps (cumulative flows, flow intensities, bivariate choropleths, heatmaps, stability/assortativity diagnostics)

## Structure (high level)

Packages: installs/loads required R packages in one chunk

Functions:

- f_generate_matrix() + f_table_to_full_matrix() for flow matrices

- f_poisson_fitting() / f_poisson_fitness() for Poisson intensity + CI helper

- f_skellam_analysis() for arrival/exit intensities and net migration

diaspora model helpers: f_pull_push_rate(), f_diaspora_analysis(), f_diaspora_half_life()

appendix plotting helpers (f_plotappendix_) and mapping helpers (f_map, f_map_bivariate_choropleth())

Data connection: reads warehouse credentials via config::get("datawarehouse")

Data preparation: cleans citizenship codes, adds age & age categories, joins municipality typologies, joins asylum status ranges, filters invalid municipality codes

Spatial: unzips and reads the Austria shapefile, simplifies geometry for plotting

## Requirements

R (recommended: R >= 4.2)

A working PostgreSQL connection with access to the referenced tables

A config.yml providing a datawarehouse entry (host, port, dbname, user, password, options)

Local helper files referenced in the script, e.g.:

- gemliste_knz.csv

- austria_municipalities_classification.csv

- austria_municipalities_typology.csv

- municipality_planning/modified_country_classification.csv

- municipality_planning/postal_municipality_nuts3.csv: 

- OGDEXT_GEM_1_STATISTIK_AUSTRIA_20240101.zip: Austria shape-file bundle

## How to run

1. Add your warehouse credentials to config.yml (under datawarehouse).

2. Ensure the required CSVs and the Austria shapefile ZIP are present in the expected paths.

3. Knit the R Markdown file (HTML output with TOC is configured in the YAML header).

ATTENTION: Most analysis and figure production lives in chunks marked eval = FALSE (appendix/paper output). Set those to eval = TRUE (or run chunks manually) to reproduce plots in P1_plots/....

## Notes

- “Arrivals” are defined as the first observed residence entry per person (after filtering), while “Exits” are derived from the last observed spell for non-current persons (and can optionally exclude deaths).

- f_generate_matrix() supports filtering by grouping variables (e.g., singular_citizenship, municipality_code, state_urbanisation, age_category) and includes options to drop camps/births/deaths.

- Internal movement logic (inter_area_movement) is included but disabled by default (eval = FALSE).

## Author

Carsten Källner
