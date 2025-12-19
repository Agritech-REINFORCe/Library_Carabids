# Library_Carabids

Library_Carabids is a reference library on ground beetles (Coleoptera: Carabidae)
developed within the REINFORCe project (AGRITECH, Spoke 2). It makes integrated
soil-biodiversity data accessible by combining morphological, ecological and molecular
information in a single, easy-to-explore resource.

## Why this library
Soil biodiversity is a key indicator of agroecosystem health. Carabids are strong
bioindicators, making it possible to read the effects of land use and farming practices on
faunal communities. This library provides a concrete and transparent baseline to monitor
and compare agricultural systems over time.

## How it was built (in brief)
- Standardized sampling in vineyards and olive groves in Veneto and Calabria
- Comparison between organic and conventional management
- Pitfall trap collection and morphological identification
- COI DNA barcoding with alignments against BOLD and NCBI
- Ecological traits assigned to interpret species functionally

## What you will find
An initial reference library with:
- 42 sequences (unique key `IDseq`) across 12 species
- Sampling metadata (area, land use, management)
- Taxonomic assignments with molecular database matches
- Ecological traits for functional interpretation

## Interactive dashboard
Explore the data here: https://agritech-reinforce.github.io/Library_Carabids/

The dashboard is organized into four sections:
- Overview: general picture of samples and traits
- Distribution: spatial and management patterns
- Sequences: sequence-level details and identifications
- DNA Metrics: sequence quality indicators

On the Overview page you will find:
- Map of sampling sites in Italy
- Sample table with ID sample, IDseq, region, land use and management
- Quick filter by region and a clear-filters button
- KPI cards (samples, sequences, regions involved)
- Donut charts for samples by land use and by farming system

On the DNA Metrics page you will find:
- Map of sites with combined filters for region, land use and farming system
- Sequence table with quality metrics (BP, B assigned, B gap, B edited and percentages)
- Stacked bar chart showing assigned, gap and edited fractions by sequence

## Repository structure
- `data/REINFORCe_sequenze_carabidi.csv`: DNA sequences
- `data/Taxonomy.csv`: taxonomy and alignment results
- `data/EcologicalTraits.csv`: ecological traits
- `data/Metadata.csv`: sampling metadata
- `docs/index.html`: support page for the dashboard embed

## CSV content
`data/REINFORCe_sequenze_carabidi.csv`
- IDseq: unique sample identifier
- Nucleotide: COI sequence

`data/Taxonomy.csv`
- IDseq
- Kingdom, Phylum, Class, Order, Family, Genus, Species
- BOLDAlingnment, BOLDAcc, NCBIAlignment, NCBIAcc

`data/EcologicalTraits.csv`
- IDseq
- FeedingStrategy, Wing, Biogeography, BodyLength
- Soil, EcologicalTolerance, BreedingSeason
- FeedNote, BiogeographyDesc

`data/Metadata.csv`
- IDseq
- Lat, Lon
- Region, LandUse, FarmingSystem
- Sample, Method

## How to query the library (Python and R)
This repository is public. You can access the data by cloning the repo or downloading
the ZIP from GitHub.

Clone with Git:
```
git clone https://github.com/Agritech-REINFORCe/Library_Carabids.git
```

### Python (pandas)
Load and join the tables on `IDseq`:
```python
import pandas as pd

seq = pd.read_csv("data/REINFORCe_sequenze_carabidi.csv")
tax = pd.read_csv("data/Taxonomy.csv", sep=";", encoding="latin-1")
traits = pd.read_csv("data/EcologicalTraits.csv", sep=";", encoding="latin-1")
meta = pd.read_csv("data/Metadata.csv", sep=";", encoding="latin-1")

df = seq.merge(tax, on="IDseq").merge(traits, on="IDseq").merge(meta, on="IDseq")

# example: number of species by land use
species_by_landuse = df.groupby("LandUse")["Species"].nunique()
print(species_by_landuse)
```

Filter by region and farming system:
```python
veneto_org = df[(df["Region"] == "Veneto") & (df["FarmingSystem"] == "Organic farm")]
print(veneto_org[["IDseq", "Species", "LandUse"]].head())
```

Summarize functional traits:
```python
traits_summary = (
    df.groupby("FeedingStrategy")["Species"]
    .nunique()
    .sort_values(ascending=False)
)
print(traits_summary)
```

### R (tidyverse)
Load and join the tables on `IDseq`:
```r
library(readr)
library(dplyr)

seq <- read_csv("data/REINFORCe_sequenze_carabidi.csv")
tax <- read_delim("data/Taxonomy.csv", delim = ";", locale = locale(encoding = "latin1"))
traits <- read_delim("data/EcologicalTraits.csv", delim = ";", locale = locale(encoding = "latin1"))
meta <- read_delim("data/Metadata.csv", delim = ";", locale = locale(encoding = "latin1"))

df <- seq %>%
  left_join(tax, by = "IDseq") %>%
  left_join(traits, by = "IDseq") %>%
  left_join(meta, by = "IDseq")

# example: number of species by land use
species_by_landuse <- df %>%
  group_by(LandUse) %>%
  summarise(n_species = n_distinct(Species))
print(species_by_landuse)
```

Filter by region and farming system:
```r
veneto_org <- df %>%
  filter(Region == "Veneto", FarmingSystem == "Organic farm") %>%
  select(IDseq, Species, LandUse)
print(veneto_org)
```

Summarize functional traits:
```r
traits_summary <- df %>%
  group_by(FeedingStrategy) %>%
  summarise(n_species = n_distinct(Species)) %>%
  arrange(desc(n_species))
print(traits_summary)
```

## Project references
- Title: GeneRare nuove EvIdenze e sfruttare Nuove tecnologIe per raFforzare il ruolO
  della biodiversita in agriColtura
- Acronym: REINFORCe
- Lead institution: Universita degli Studi di Verona
- Duration: June 2024 - August 2025

## License
Open access. If you use these data, please cite them as:
Data obtained from the REINFORCe project, "GeneRare nuove EvIdenze e sfruttare Nuove
tecnologIe per raFforzare il ruolO della biodiversita in agriColtura", funded by the
European Union - NextGenerationEU within the PNRR (Italia Domani), MUR.
