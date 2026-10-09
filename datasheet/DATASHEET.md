# Datasheet - Tropical Cyclone RI Dataset (IBTrACS + ERA5 + GridSat-B1)
> The following datasheet format and content is taken from Gebru et al., [_Datasheets for Datasets (2021)_](https://arxiv.org/abs/1803.09010)
> and the repository by [Cao et. al(2020)](https://github.com/TristaCao/into_inclusivecoref/blob/master/GICoref/datasheet-gicoref.md).

This dataset describes tropical cyclones every 6 hours from 1980 to 2025 and is used to predict rapid intensification (RI): an increase in maximum sustained wind of at least 30 kt in the next 24 hours (Kaplan & DeMaria, 2003). It combines three public sources:

- **IBTrACS v04r01** (Knapp et al., 2010): storm positions and intensities. The notebook downloads this directly from NOAA NCEI.
- **ERA5** (Hersbach et al., 2020): environmental features (sea surface temperature, sea level pressure, water vapor, and wind shear) around each storm. Precomputed and published as `era5_features.parquet`.
- **GridSat-B1** (Knapp et al., 2011): cold-cloud fractions from infrared satellite images near each storm center. Precomputed and published as `satellite_features.parquet`.

The two precomputed files are published in the [`dataset_v4` GitHub release](https://github.com/ccai-tropicalcycloneri-tutorial-2026/tropicalcycloneriprediction/releases/tag/dataset_v4). The notebook also shows step by step how the features are computed for one example observation (Hurricane Katrina, 2005).

## Motivation
- **For what purpose was the dataset created?** To teach how to build an ML model for 24-hour RI prediction from public, physically meaningful features, in the spirit of the Statistical Hurricane Intensity Prediction Scheme (SHIPS; DeMaria & Kaplan, 1999). Precomputing the ERA5 and satellite features lets learners run the tutorial without downloading large gridded datasets.
- **Who created this dataset (e.g., which team, research group) and on behalf of which entity (e.g., company, institution, organization)?** [Anonymized for review.] The underlying data were created by NOAA NCEI (IBTrACS, GridSat-B1) and ECMWF / Copernicus Climate Change Service (ERA5).
- **Funding/support** [Anonymized for review.]

## Composition
- **What do the instances that comprise the dataset represent (e.g., documents, photos, people, countries)?** Each instance is one tropical cyclone observation: one storm at one 6-hourly synoptic time (00, 06, 12, or 18 UTC).
- **How many instances are there in total (of each type, if appropriate)?** 78,472 labeled observations from 4,105 storms (1980–2025), of which 6,177 (7.9%) are RI.
- **Does the dataset contain all possible instances or is it a sample (not necessarily random) of instances from a larger set?** It is a filtered subset of IBTrACS since 1980. We keep main tracks, standard 6-hourly synoptic times, tropical systems (`nature == 'TS'`), observations over water (`dist2land > 0`), and observations with a USA agency wind value. Observations without a known wind 24 hours later are dropped because their label is unknown (about 19% of filtered rows). Storms approaching land or becoming extratropical are therefore under-represented, which is standard in RI studies.
- **What data does each instance consist of?** Derived features, not raw data:
  - *IBTrACS:* current wind, Saffir-Simpson category, distance to land, storm speed, location and day of year (encoded with sine/cosine), and wind values and changes 6, 12, and 24 hours earlier.
  - *ERA5:* sea surface temperature, sea level pressure, and total column water vapor averaged within 200 km of the storm center; 200 hPa and 850 hPa winds and vertical wind shear averaged over a 200–800 km ring.
  - *GridSat-B1:* fraction of infrared pixels colder than 220 K within 100 km, and colder than 235 K within 200 km.
- **Is there a label or target associated with each instance?** Yes. `ri_tplus24 = 1` if the USA agency maximum wind increases by at least 30 kt over the next 24 hours, otherwise 0. The label is computed in the notebook from IBTrACS.
- **Is any information missing from individual instances?** The ERA5 file has no missing values. In the satellite file, about 2.5% of observations have no cold-cloud fraction (1,998 at 100 km and 2,023 at 200 km), mainly because of gaps in satellite coverage. Lagged wind features are missing when there is no observation at the exact earlier time. XGBoost handles these missing values natively, and logistic regression uses median imputation.
- **Are relationships between individual instances made explicit (e.g., users' movie ratings, social network links)?** Yes. Observations from the same storm share a storm ID (`sid`) and are ordered by time (`iso_time`).
- **Are there recommended data splits (e.g., training, development/validation, testing)?** Yes, by season: training 1980–2010 (57,178 observations), validation 2011–2015 (7,496), and test 2016–2025 (13,798). No storm appears in more than one split. Model selection uses 5-fold cross-validation on the training years, grouped by storm.
- **Are there any errors, sources of noise, or redundancies in the dataset?** Best-track intensities are estimates and can be revised after a season. Observing methods (satellites, aircraft reconnaissance) have improved over time, which may affect how RI is recorded. ERA5 is a model-based reanalysis and does not fully resolve the storm core. Satellite coverage and quality vary by region and year. Several features are strongly related to each other (for example, current and lagged wind).
- **Is the dataset self-contained, or does it link to or otherwise rely on external resources (e.g., websites, tweets, other datasets)?** It relies on external resources. IBTrACS is downloaded live from NOAA NCEI, so newer IBTrACS updates may change the data slightly. The ERA5 example in the notebook reads the public ARCO-ERA5 store on Google Cloud. The precomputed feature files are hosted in this repository's GitHub release. Terms of use of each source apply (see Distribution).
- **Does the dataset contain data that might be considered confidential (e.g., data that is protected by legal privilege or by doctor–patient confidentiality, data that includes the content of individuals' non-public communications)?** No.
- **Does the dataset contain data that, if viewed directly, might be offensive, insulting, threatening, or might otherwise cause anxiety?** No. It describes physical storm properties. Some storms caused loss of life, which the tutorial mentions for context.

The dataset does not relate to people, so the remaining questions in this section are not applicable.

## Collection Process
- **How was the data associated with each instance acquired**? Derived from public datasets. IBTrACS combines best tracks from forecasting agencies. ERA5 combines observations with a weather model. GridSat-B1 combines geostationary infrared satellite observations. Our features are averages over fixed areas around each IBTrACS storm position. The notebook checks the precomputed ERA5 features against a step-by-step calculation for one observation, and the values match.
- **What mechanisms or procedures were used to collect the data (e.g., hardware apparatuses or sensors, manual human curation, software programs, software APIs)**? Python scripts (xarray, NumPy, pandas) that read IBTrACS, ERA5, and GridSat-B1 and compute area averages at each storm time.
- **If the dataset is a sample from a larger set, what was the sampling strategy (e.g., deterministic, probabilistic with specific sampling probabilities)**? Deterministic filtering, as described under Composition.
- **Who was involved in the data collection process (e.g., students, crowdworkers, contractors) and how were they compensated (e.g., how much were crowdworkers paid)?** The tutorial authors. No crowdworkers were involved.
- **Over what timeframe was the data collected?** Storm seasons 1980–2025. The features were computed in 2026.
- **Were any ethical review processes conducted (e.g., by an institutional review board)?** Not applicable. The data contain no information about people.

## Preprocessing/cleaning/labeling
- **Was any preprocessing/cleaning/labeling of the data done (e.g., discretization or bucketing, tokenization, part-of-speech tagging, SIFT feature extraction, removal of instances, processing of missing values)?** Yes: the filters listed under Composition, spatial averaging of ERA5 and GridSat-B1 fields, sine/cosine encoding of location and day of year, lagged wind features, and the RI label. All filtering and labeling steps are shown in the notebook.
- **Was the "raw" data saved in addition to the preprocessed/cleaned/labeled data (e.g., to support unanticipated future uses)?** The raw data are publicly archived by their providers: [IBTrACS](https://www.ncei.noaa.gov/products/international-best-track-archive), [ERA5](https://cds.climate.copernicus.eu/datasets/reanalysis-era5-single-levels), and [GridSat-B1](https://www.ncei.noaa.gov/products/gridded-geostationary-brightness-temperature).
- **Is the software that was used to preprocess/clean/label the data available?** The filtering, labeling, and the feature calculation for an example observation are in `notebook/tropicalcyclone_rapidintensification.ipynb`.

## Uses
- **Has the dataset been used for any tasks already?** Yes, for 24-hour RI prediction in this tutorial.
- **Is there a repository that links to any or all papers or systems that use the dataset?** This repository.
- **What (other) tasks could the dataset be used for?** Predicting intensity change as a number (regression), other RI thresholds or time windows, comparing basins, or studying how storm environments relate to intensification.
- **Is there anything about the composition of the dataset or the way it was collected and preprocessed/cleaned/labeled that might impact future uses?** ERA5, final best tracks, and GridSat-B1 become available only after a delay, so models trained on this dataset are not directly usable in real time. Storms near landfall or becoming extratropical are excluded. The RI rate rises over time (7.0% in training, 10.2% in test years), so models should be evaluated on recent years.
- **Are there tasks for which the dataset should not be used?** Real-time warnings or any safety-critical decisions. Official forecasts from agencies such as the National Hurricane Center should always be used for those.

## Distribution
- **Will the dataset be distributed to third parties outside of the entity (e.g., company, institution, organization) on behalf of which the dataset was created?** Yes, publicly, as part of this tutorial.
- **How will the dataset will be distributed (e.g., tarball on website, API, GitHub)? Does the dataset have a digital object identifier (DOI)?** As Parquet files in a GitHub release (`dataset_v4`). No DOI.
- **When will the dataset be distributed?** It is available now.
- **Will the dataset be distributed under a copyright or other intellectual property (IP) license, and/or under applicable terms of use (ToU)?** The repository is under the MIT License. Users must also follow the terms of the source data: IBTrACS and GridSat-B1 (NOAA NCEI) and ERA5 (Copernicus Climate Change Service licence, see the [CDS dataset page](https://cds.climate.copernicus.eu/datasets/reanalysis-era5-single-levels)).
- **Have any third parties imposed IP-based or other restrictions on the data associated with the instances?** Only the source data terms listed above.
- **Do any export controls or other regulatory restrictions apply to the dataset or to individual instances?** None known.

## Maintenance
- **Who will be supporting/hosting/maintaining the dataset?** The tutorial authors, through this GitHub repository.
- **How can the owner/curator/manager of the dataset be contacted (e.g., email address)?** Through GitHub issues on this repository.
- **Is there an erratum?** No.
- **Will the dataset be updated (e.g., to correct labeling errors, add new instances, delete instances)?** Possibly, as a new release (for example, to add new seasons). Updates will be announced in the repository.
- **Will older versions of the dataset continue to be supported/hosted/maintained?** Older releases stay available on GitHub.
- **If others want to extend/augment/build on/contribute to the dataset, is there a mechanism for them to do so?** Yes, through pull requests or issues on this repository.

## References
- DeMaria, M., & Kaplan, J. (1999). An Updated Statistical Hurricane Intensity Prediction Scheme (SHIPS) for the Atlantic and Eastern North Pacific Basins. *Weather and Forecasting*, 14(3), 326–337. https://doi.org/10.1175/1520-0434(1999)014<0326:AUSHIP>2.0.CO;2
- Kaplan, J., & DeMaria, M. (2003). Large-Scale Characteristics of Rapidly Intensifying Tropical Cyclones in the North Atlantic Basin. *Weather and Forecasting*, 18(6), 1093–1108. https://doi.org/10.1175/1520-0434(2003)018<1093:LCORIT>2.0.CO;2
- Knapp, K. R., et al. (2010). The International Best Track Archive for Climate Stewardship (IBTrACS). *Bulletin of the American Meteorological Society*, 91(3), 363–376. https://doi.org/10.1175/2009BAMS2755.1
- Knapp, K. R., et al. (2011). Globally Gridded Satellite Observations for Climate Studies. *Bulletin of the American Meteorological Society*, 92(7), 893–907. https://doi.org/10.1175/2011BAMS3039.1
- Hersbach, H., et al. (2020). The ERA5 global reanalysis. *Quarterly Journal of the Royal Meteorological Society*, 146(730), 1999–2049. https://doi.org/10.1002/qj.3803
