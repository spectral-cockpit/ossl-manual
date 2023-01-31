# Neospectra database

::: {.rmdnote}
You are reading the work-in-progress of the SoilSpec4GG manual. This chapter is currently draft version, a peer-review publication is pending.
:::

A persistent copy of the database is hosted on [Zenodo](https://doi.org/10.5281/zenodo.7586622).

## Overview

Recent advances in hardware technology have enabled the development of handheld sensors with similar performance specifications as laboratory-grade near-infrared (NIR) spectrometers.

We have compiled a hand-held NIR spectral library using the NeoSpectra Handheld NIR Analyzer developed by [Si-Ware](https://www.si-ware.com/). Each scanner is fitted with Fourier-Transform technology based on the semiconductor Micro Electromechanical Systems (MEMS) manufacturing technique, promising accuracy, and consistency between devices.

This library includes 2,106 distinct mineral soil samples scanned across 9 of these portable low-cost NIR spectrometers (indicated by serial no). 2,016 of these soil samples were selected to represent the diversity of mineral soils found in the United States, and 90 samples were selected across Ghana, Kenya, and Nigeria.

519 of the US samples were selected and scanned by [Woodwell Climate Research Center](https://www.woodwellclimate.org/). These samples were queried from the [USDA NRCS NSSC-KSSL Soil Archives](https://ncsslabdatamart.sc.egov.usda.gov/) as having a complete set of eight measured properties (TC, OC, TN, CEC, pH, clay, sand, and silt). They were stratified based on the major horizon and taxonomic order, omitting the categories with less than 500 samples. Three percent of each stratum (i.e., a combination of major horizon and taxonomic order) was then randomly selected as the final subset retrieved from KSSL's physical soil archive as 2-mm sieved samples.

The remaining 1,604 US samples were queried from the USDA NRCS NSSC-KSSL Soil Archives by the [University of Nebraska - Lincoln](University of Nebraska - Lincoln) to meet the following criteria: Lower depth <= 30 cm, pH range 4.0 to 9.5, Organic carbon <10%, Greater than lower detection limits, Actual physical samples available in the archive, Samples collected and analyzed from 2001 onwards, Samples having complete analyses for high-priority properties (Sand, Silt, Clay, CEC, Exchangeable Ca, Exchangeable Mg, Exchangeable K, Exchangeable Na, CaCO3, OC, TN), & MIR scanned.

All samples were scanned dry 2mm sieved. ~20g of sample was added to a plastic weighing boat where the NeoSpectra scanner would be placed down to make direct contact with the soil surface. The scanner was gently moved across the surface of the sample as 6 replicate scans were taken. These replicates were then averaged so that there is one spectra per sample per scanner in the resulting database.

## Spectral diversity
