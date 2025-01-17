# OSSL database access

::: {.rmdnote}
You are reading the work-in-progress of the SoilSpec4GG manual. This chapter is currently draft version, a peer-review publication is pending.
:::

The OSSL is distributed through Google Could storage bucket, MongoDB, and API. Google Cloud storage bucket hosts static files in two formats: compressed csv (`.csv.gz`) and `qs` (from qs R package). The `csv.gz` is intended to work across different platforms, while `qs` is the preferred format for being used within R. MongoDB and the API can be used to construct requests to fetch data with specific filters (e.g. region, dataset source, etc.), differently from the static files where you need to download the whole database.

In the OSSL, some original dataset share common ids across the VisNIR and MIR range. Some ids, however, have only one range represented (either VisNIR or MIR). OSSL is a tabular database keeping at least one spectral range. A filter must be run before using the database to remove observations with missing spectra for a desired range.

To access an extend global compilation of soil legacy point data sets, refer to <https://github.com/OpenGeoHub/SoilSamples> repository.

## Google Cloud Storage {.unnumbered}

The datasets in the public bucket can be updated without notice. One can both run the link on a browser to download the files, or provide the URLs in a programming language to automatically fecth them.

Use the following URLs to access the whole database levels:

**Compressed csv**  
<https://storage.googleapis.com/soilspec4gg-public/ossl_all_L0_v1.2.csv.gz>  
<https://storage.googleapis.com/soilspec4gg-public/ossl_all_L1_v1.2.csv.gz>  

**qs format (preferred on R)**  
<https://storage.googleapis.com/soilspec4gg-public/ossl_all_L0_v1.2.qs>  
<https://storage.googleapis.com/soilspec4gg-public/ossl_all_L1_v1.2.qs>  

Use these alternative URLs to access the OSSL as separate files:

**Compressed csv**  
<https://storage.googleapis.com/soilspec4gg-public/ossl_soilsite_L0_v1.2.csv.gz>  
<https://storage.googleapis.com/soilspec4gg-public/ossl_soillab_L0_v1.2.csv.gz>  
<https://storage.googleapis.com/soilspec4gg-public/ossl_soillab_L1_v1.2.csv.gz>  
<https://storage.googleapis.com/soilspec4gg-public/ossl_mir_L0_v1.2.csv.gz>  
<https://storage.googleapis.com/soilspec4gg-public/ossl_visnir_L0_v1.2.csv.gz>  

**qs format (preferred on R)**  
<https://storage.googleapis.com/soilspec4gg-public/ossl_soilsite_L0_v1.2.qs>  
<https://storage.googleapis.com/soilspec4gg-public/ossl_soillab_L0_v1.2.qs>  
<https://storage.googleapis.com/soilspec4gg-public/ossl_soillab_L1_v1.2.qs>  
<https://storage.googleapis.com/soilspec4gg-public/ossl_mir_L0_v1.2.qs>  
<https://storage.googleapis.com/soilspec4gg-public/ossl_visnir_L0_v1.2.qs>  

Example with R. Use `dataset.code_ascii_txt` and `id.layer_uuid_txt.` as joining columns:

```r
## Packages
library("tidyverse")
library("curl")
library("qs")

## Separate files
soil <-  "https://storage.googleapis.com/soilspec4gg-public/ossl_soillab_L1_v1.2.qs"
soil <- curl_fetch_memory(soil)
soil <- qdeserialize(soil$content)
mir <- "https://storage.googleapis.com/soilspec4gg-public/ossl_mir_L0_v1.2.qs"
mir <- curl_fetch_memory(mir)
mir <- qdeserialize(mir$content)

## Join
ossl <- left_join(mir, soil, by = c("dataset.code_ascii_txt", "id.layer_uuid_txt"))
```

## MongoDB {.unnumbered}  

Available collections:  

- **soilsite**: site information, e.g., coordinates, pedons, layers, etc.  
- **soillab_L0**: original soil laboratory data (wet chemistry), e.g., soil texture, carbon, etc.  
- **soillab_L1**: harmonized soil laboratory data (wet chemistry), e.g., soil texture, carbon, etc.  
- **mir**: MIR spectral data in the 600-4000 cm<sup>-1</sup> range with metadata.  
- **visnir**:  VisNIR spectral data in the 350-2500 nm range with metadata.  
- **ossl_L0**:  Whole OSSL with all separate tables joined at level 0.  
- **ossl_L1**:  Whole OSSL with all separate tables joined at level 1.  

>**<span style="color:orange;">NOTE: Within the MongoDB, all the dots in column names are replaced by underscore.</span>**

Accessing in R:

```r
## Packages and helping functions

library(mongolite)
library(jsonify)

soilspec4gg.db = list(
  host = 'api.soilspectroscopy.org',
  name = 'soilspec4gg',
  user = 'soilspec4gg',
  pw = 'soilspec4gg'
)

soilspec4gg.db$url <- paste0(
  'mongodb://', soilspec4gg.db$user, ':', 
  soilspec4gg.db$pw, '@', 
  soilspec4gg.db$host, '/', 
  soilspec4gg.db$name, '?ssl=true'
)

soilspec4gg.init <- function() {
  print('Creating the access for mongodb collections.')
  soilspec4gg.db$collections <<- list(
    soilsite = mongo(collection = 'soilsite', url = soilspec4gg.db$url, verbose = TRUE),
    soillab_L0 = mongo(collection = 'soillab_L0', url = soilspec4gg.db$url, verbose = TRUE),
    soillab_L1 = mongo(collection = 'soillab_L1', url = soilspec4gg.db$url, verbose = TRUE),
    mir = mongo(collection = 'mir', url = soilspec4gg.db$url, verbose = TRUE),
    visnir = mongo(collection = 'visnir', url = soilspec4gg.db$url, verbose = TRUE),
    ossl_L0 = mongo(collection = 'ossl_L0', url = soilspec4gg.db$url, verbose = TRUE),
    ossl_L1 = mongo(collection = 'ossl_L1', url = soilspec4gg.db$url, verbose = TRUE)
  )  
}

## Accessing the database

# Initialization
soilspec4gg.init()

# Checking available collections
names(soilspec4gg.db$collections)

# Get all records for soilsite table
soilspec4gg.db$collections$soilsite$count("{}")

# Read all data from a collection back to the R environment
soilsite <- soilspec4gg.db$collections$soilsite$find('{}')

# Getting unique values for a field
soilspec4gg.db$collections$soilsite$distinct(key = "dataset_code_ascii_txt")

# Getting column names
names(soilspec4gg.db$collections$ossl_L0$find('{}', limit = 1))[1:20]
names(soilspec4gg.db$collections$ossl_L1$find('{}', limit = 1))[1:20]
names(soilspec4gg.db$collections$soilsite$find('{}', limit = 1))
names(soilspec4gg.db$collections$soillab_L0$find('{}', limit = 1))
names(soilspec4gg.db$collections$soillab_L1$find('{}', limit = 1))
names(soilspec4gg.db$collections$mir$find('{"dataset_code_ascii_txt": "KSSL.SSL"}', limit = 1))[1:20]
names(soilspec4gg.db$collections$visnir$find('{"dataset_code_ascii_txt": "LUCAS.SSL"}', limit = 1))[1:20]

# Query a specific dataset
soilsite.serbia <- soilspec4gg.db$collections$soilsite$find('{"dataset_code_ascii_txt" : "SERBIA.SSL"}')

# Filtering by ID
soilspec4gg.db$collections$soilsite$find('{"id_layer_uuid_txt": "50d0bfaaf50feb0ba2c8a3aa2cd788bb"}')
```

## API {.unnumbered}

[OSSL API](https://api.soilspectroscopy.org/__docs__/#/) (Application Programming Interface) is also available and can be used to construct requests to fetch data, models and generate predictions. The outputs of predictions can be obtained as [JSON](https://www.json.org/json-en.html) or CSV files, making the system fully interoperable. The OSSL API is at the moment based on using the [plumber package](https://www.rplumber.io/) and is **provided for testing purposes only**. Users can make predictions with pretrained models for 20 spectra per request, but these limits will be gradually extended.

<div class="figure" style="text-align: center">
<img src="img/preview_ossl_api_swagger.png" alt="OSSL API is available for testing." width="100%" />
<p class="caption">(\#fig:ossl-api)OSSL API is available for testing.</p>
</div>
