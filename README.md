# District-Level Mapping and Hazard Assessment of Extreme Rainfall in Maharashtra

## Overview

This project develops a **district- and pixel-level extreme rainfall hazard assessment framework for Maharashtra** using long-term gridded rainfall data, extreme-value analysis, exceedance probability, percentile thresholds, and a composite hazard index.

The workflow combines:

- Annual Maximum Series (AMS)
- Gumbel Type-I Extreme Value Distribution
- Weibull plotting-position / exceedance probability analysis
- P95 and P99 percentile thresholds
- Extreme-event frequency
- Pixel-wise hazard-index computation
- District-level zonal statistics
- Low / Medium / High hazard classification
- Validation using 2021 rainfall
- Sensitivity analysis of hazard-index weights
- Additional 2005 and 2024 rainfall analysis

The study uses **1991–2020 IMD gridded daily rainfall** as the primary analysis period, with **2021 rainfall used for validation**. The project report states that the rainfall grid has a spatial resolution of approximately **27.75 km**. Maharashtra boundaries and district boundaries are obtained from Survey of India shapefiles.

---

## Project Information

**Course:** CM801 – Introduction to Risk Analysis  
**Project:** Group Assignment  
**Study Area:** Maharashtra, India  
**Primary Analysis Period:** 1991–2020  
**Validation Year:** 2021

### Group Members

| Roll No. | Name | Contribution |
|---|---|---|
| 21D180038 | Shriya Dilip Kajrolkar | AMS and Gumbel Distribution Analysis; Validation of Hazard Mapping Results |
| 25M0611 | Rishi Ganesh L | Methodology framework; Gumbel Distribution Analysis; Validation; Review of analyses; Report Drafting |
| 25M0612 | Jagu Manish | Data Preparation; Validation of Weibull plotting with Gumbel data |
| 25M0613 | Shakthi Sriram S | AMS Analysis; Hazard Mapping Analysis |
| 25M0614 | Minti Jasakiya | Exceedance Probability Analysis; Report Drafting |
| 25M0615 | Elavarasan M | Percentile Threshold Analysis; Hazard Mapping Analysis |

The assignment report identifies Prof. Subhankar Karmakar, Center for Climate Studies, IIT Bombay, as the supervisor.

---

# Objectives

The main objective is to identify spatial patterns of **extreme rainfall hazard across Maharashtra** by combining rainfall intensity and frequency.

Specific objectives are to:

1. Derive annual maximum rainfall at each rainfall-grid cell.
2. Analyse extreme rainfall using the Gumbel Type-I distribution.
3. Estimate rainfall magnitudes for different return periods.
4. Estimate empirical exceedance probabilities using the Weibull plotting position.
5. Compare empirical Weibull results with theoretical Gumbel estimates.
6. Define spatially varying extreme rainfall thresholds using P95 and P99.
7. Quantify the frequency of extreme rainfall events.
8. Develop a composite rainfall hazard index.
9. Aggregate pixel-level hazard to district level.
10. Classify districts into Low, Medium and High hazard categories.
11. Validate the hazard pattern using independent 2021 rainfall.
12. Test the sensitivity of the hazard map to different intensity/frequency weights.

---

# Study Area

The study covers **Maharashtra**, a state with strong spatial variability in rainfall.

The Western Ghats and Konkan coastal belt receive substantially higher rainfall, while the interior Deccan plateau generally receives lower rainfall. This west–east rainfall gradient makes Maharashtra suitable for studying spatial differences in extreme rainfall.

The study-area map in the project presentation shows Maharashtra within India and the district boundaries used for district-level analysis.

---

# Data

## Primary Rainfall Dataset

The main dataset is daily gridded rainfall from the **Indian Meteorological Department (IMD)**.

| Variable | Source | Spatial Resolution | Temporal Resolution | Period |
|---|---|---:|---|---|
| Daily precipitation | IMD | 0.25° (~27.75 km) | Daily | 1991–2020 |

The report states that the original rainfall data were downloaded for the Indian subcontinent and clipped to Maharashtra using the Maharashtra boundary shapefile.

## Validation Data

Daily IMD rainfall for **2021** is used as an independent validation dataset.

## Boundary Data

Two types of vector boundaries are used:

- Maharashtra state boundary
- Maharashtra district boundaries

The district boundary shapefile is used for zonal statistics and district-level hazard classification.

---

# Software and Python Libraries

The main notebook is designed for **Google Colab** and begins by installing:

```bash
pip install h5netcdf rioxarray geopandas rasterstats imdlib cartopy
```

The workflow uses the following Python libraries:

```text
xarray
numpy
pandas
matplotlib
scipy
geopandas
rasterio
rioxarray
rasterstats
imdlib
cartopy
h5netcdf
```

### Main roles

| Library | Purpose |
|---|---|
| `xarray` | NetCDF and multidimensional rainfall processing |
| `numpy` | Numerical and array operations |
| `pandas` | Tabular data and district statistics |
| `scipy` | Gumbel fitting and statistical calculations |
| `geopandas` | Maharashtra and district shapefiles |
| `rasterio` | Raster operations |
| `rioxarray` | CRS, spatial dimensions, clipping and raster export |
| `rasterstats` | District-level zonal statistics |
| `imdlib` | Download/access IMD rainfall data |
| `matplotlib` | Plots and rainfall/hazard maps |
| `cartopy` | Geographic plotting |
| `h5netcdf` | NetCDF backend support |

---

# Repository Structure

A recommended repository structure is:

```text
.
├── README.md
├── CM801_Group_Assignment.ipynb
│
├── data/
│   ├── Maharashtra_Rainfall_1991_2020.nc
│   ├── annual_max_rainfall.nc
│   ├── gumbel_return_periods.nc
│   ├── exceedance_probability.nc
│   └── ...
│
├── Shapefiles/
│   ├── MH.shp
│   ├── MH_Districts_SOI.shp
│   └── supporting shapefile files
│
└── outputs/
    ├── mean_annual_max_rainfall.tif
    ├── district_rainfall_stats.shp
    ├── district_rainfall_stats.csv
    ├── gumbel_return_maps.png
    ├── weibull_return_maps.png
    ├── hazard_index_pixel.png
    ├── district_hazard_map.png
    └── ...
```

The original notebook uses Google Drive paths under:

```text
/content/drive/MyDrive/CM801/
```

These paths must be changed when running the notebook outside the original Google Colab environment.

---

# Methodology

The complete workflow is:

```text
IMD Daily Rainfall (1991–2020)
             │
             ▼
     Clip to Maharashtra
             │
             ▼
 Annual Maximum Series (AMS)
             │
      ┌──────┼───────────────┐
      │      │               │
      ▼      ▼               ▼
   Gumbel  Weibull        P95 / P99
 Distribution  Exceedance   Thresholds
      │      Probability       │
      │      Analysis          │
      └──────┬─────────────────┘
             │
             ▼
   Extreme Rainfall Intensity
             +
    Extreme Event Frequency
             │
             ▼
       Hazard Index
             │
       ┌─────┴─────┐
       ▼           ▼
 Pixel-level   District-level
   Hazard       Zonal Statistics
                   │
                   ▼
          Low / Medium / High
             Classification
                   │
                   ▼
              Validation
                   │
                   ▼
          Sensitivity Analysis
```

---

# 1. Annual Maximum Series (AMS)

The first step is to convert daily rainfall into an **Annual Maximum Series**.

For every rainfall-grid cell:

```text
AMS(year, pixel) = maximum daily rainfall in that year
```

For the 1991–2020 period, this produces up to 30 annual maximum values per grid cell.

The notebook performs this using:

```python
annual_max = ds['RAINFALL'].resample(TIME='1Y').max()
```

The resulting dataset is saved as:

```text
annual_max_rainfall.nc
```

The report describes AMS as one extreme rainfall value per year and uses it as the basis for the subsequent extreme-value analysis.

---

# 2. Mean Annual Maximum Rainfall

The 30 annual maximum values are averaged at every grid cell:

```text
Mean AMS = (1 / 30) × Σ Annual Maximum_i
```

This provides a spatial representation of the average annual extreme rainfall.

The output is saved as:

```text
mean_annual_max_rainfall.tif
```

The resulting map shows a strong rainfall gradient, with higher extreme rainfall along the Western Ghats and Konkan coast.

---

# 3. Gumbel Extreme-Value Analysis

A **Gumbel Type-I Extreme Value Distribution** is fitted independently at each rainfall-grid cell using its 30-year AMS.

The fitted distribution is used to estimate rainfall for return periods:

```text
2 years
5 years
10 years
25 years
50 years
```

The notebook also explores a 100-year return period for selected validation/analysis steps.

For a return period `T`, the exceedance probability used for the Gumbel quantile is:

```text
P = 1 - 1/T
```

The rainfall estimate is then obtained using the fitted Gumbel distribution.

The fitted parameters are:

- `loc` — location parameter
- `scale` — scale parameter

The resulting return-level dataset is stored as:

```text
gumbel_return_periods.nc
```

---

# 4. District-Level Gumbel Statistics

Pixel-level return levels are converted into district-level information using **zonal statistics**.

For each district and each return period, the notebook calculates:

- Mean rainfall
- Maximum rainfall
- 95th percentile rainfall

The resulting district dataset is saved as:

```text
district_rainfall_stats.shp
district_rainfall_stats.csv
```

This provides administratively meaningful extreme-rainfall estimates.

The report shows that coastal districts consistently have higher Gumbel return levels across the analysed return periods.

---

# 5. AMS District Statistics

District-level statistics are also calculated directly from the mean AMS raster.

For each district:

```text
Mean AMS
Maximum AMS
99th-percentile AMS
```

are calculated.

These statistics are used to examine spatial variability in extreme rainfall independent of the Gumbel return-level maps.

---

# 6. Percentile Threshold Analysis

Two spatially varying rainfall thresholds are calculated:

```text
P95 = 95th percentile daily rainfall
P99 = 99th percentile daily rainfall
```

These thresholds are calculated **pixel-wise** rather than using one global threshold.

This is important because rainfall varies strongly across Maharashtra. A global threshold could allow high-rainfall coastal regions to dominate the analysis and may not represent local extremes consistently.

The project compares the distributions of P95 and P99.

The results show that:

- P95 values are more tightly clustered.
- P99 values have a wider spatial distribution.
- P99 captures rarer and more intense rainfall events.

Therefore, **P99 is selected as the primary intensity indicator for hazard analysis**.

---

# 7. Weibull Exceedance Probability

The project uses the **Weibull plotting position** as an empirical exceedance-probability method.

The AMS values at each pixel are:

1. Sorted in descending order.
2. Assigned ranks.
3. Converted to exceedance probability using:

```text
P = rank / (n + 1)
```

The corresponding return period is:

```text
T = 1 / P
```

The resulting data are saved as:

```text
exceedance_probability.nc
```

The notebook also generates Weibull-based rainfall estimates for:

```text
2-year
5-year
10-year
25-year
50-year
```

return periods.

---

# 8. Weibull vs Gumbel Validation

The empirical Weibull estimates are compared against the theoretical Gumbel estimates.

The comparison is performed at sample pixels and visually assesses whether the empirical observations follow the fitted Gumbel relationship.

The report states that the two approaches show consistent spatial patterns:

- Gumbel provides a smoother theoretical estimate.
- Weibull retains greater sensitivity to isolated high-intensity events.
- Both approaches identify high extreme rainfall along the western coast.

This comparison is used to support the suitability of the Gumbel distribution for the extreme-rainfall analysis.

---

# 9. Frequency of Extreme Rainfall

Frequency is calculated using the number of rainfall observations exceeding spatially varying thresholds.

The notebook computes:

```text
P95 threshold
P99.5 threshold
```

and counts the number of rainfall observations exceeding these thresholds.

The frequency component is then normalized before being combined with rainfall intensity.

The project uses a logarithmic transformation for the P99.5 frequency component:

```python
norm_99 = np.log1p(extreme_days_99)
```

This reduces the influence of very large frequency values.

---

# 10. Hazard Index

The project defines rainfall hazard as a function of:

```text
Intensity + Frequency
```

## Intensity

The intensity component is primarily based on the spatial P99 rainfall threshold.

The project presentation describes intensity as the primary driver of hazard because P99 captures the magnitude of the most extreme events.

## Frequency

Frequency represents how often rainfall exceeds the selected extreme threshold.

## Composite hazard

The baseline hazard index uses:

```text
Hazard Index =
0.6 × normalized Intensity
+
0.4 × normalized Frequency
```

The notebook normalizes the resulting hazard index to a 0–1 range.

The project therefore gives slightly greater weight to rainfall intensity while still accounting for repeated extreme events.

---

# 11. Pixel-Level Hazard Mapping

The pixel-level hazard index is mapped across Maharashtra.

The output represents spatial differences in rainfall hazard before aggregation to administrative units.

The project results show that the western coastal belt and Western Ghats have the highest hazard values, while the interior and eastern parts generally have lower values.

---

# 12. District-Level Hazard Mapping

Pixel-level hazard values are aggregated to districts using zonal statistics.

For each district, the mean hazard index is calculated.

The notebook classifies districts using fixed thresholds:

```text
0.00 – 0.25  → Low
0.25 – 0.50  → Medium
0.50 – 1.00  → High
```

The output is a district-level hazard map and a ranked district hazard table.

---

# 13. Validation

The project uses **2021 IMD rainfall** as an independent validation dataset.

The 2021 rainfall is:

1. Downloaded using `imdlib`.
2. Clipped to Maharashtra.
3. Converted to the same spatial framework.
4. Used to calculate the 2021 P99 extreme rainfall.
5. Compared against the hazard index.

The project reports a **pixel-wise Pearson correlation of 0.73** between the hazard index and 2021 P99 rainfall.

This indicates a strong positive spatial association between the derived hazard index and an independent year's extreme rainfall pattern.

---

# 14. Sensitivity Analysis

The robustness of the hazard index is tested by varying the relative weights assigned to intensity and frequency.

The notebook tests:

```text
0.5 intensity + 0.5 frequency
0.6 intensity + 0.4 frequency   ← baseline
0.7 intensity + 0.3 frequency
```

The resulting hazard maps are compared using Pearson correlation.

The report reports extremely high correlations:

```text
r = 0.995 – 0.999
```

between the alternative weighting schemes.

This indicates that moderate changes in the intensity/frequency weights do not substantially alter the spatial hazard pattern.

The report also notes that differences are generally small, with slightly larger differences occurring around the Western Ghats.

---

# 15. Additional 2005 Rainfall Analysis

The notebook contains an additional analysis of **2005 rainfall**.

It extracts:

- Annual rainfall for 2005
- Rainfall on **27 July 2005**
- District-wise annual rainfall
- District-wise rainfall for the 27 July event

The outputs include:

```text
2005_maps.png
district_2005_maps.png
```

This section is supplementary to the main 1991–2020 extreme-rainfall hazard workflow.

---

# 16. Additional 2024 Rainfall Analysis

The notebook also contains an additional analysis using **2024 IMD rainfall**.

It calculates:

- Annual rainfall for 2024
- Maximum daily rainfall for 2024
- District-wise annual rainfall
- District-wise maximum daily rainfall

The output map is:

```text
district_2024_maps.png
```

This is an additional rainfall assessment and is separate from the primary 1991–2020 hazard-index development and 2021 validation.

---

# Key Results

## Spatial rainfall pattern

The mean annual maximum rainfall analysis shows a clear west–east gradient:

- Western Ghats and Konkan coast → higher extreme rainfall
- Interior Deccan plateau → comparatively lower extreme rainfall

The district-wise AMS maps reinforce this pattern.

## Gumbel return levels

Coastal districts including:

- Ratnagiri
- Sindhudurg
- Raigad
- Mumbai

consistently show high rainfall values across the analysed return periods.

The project also finds that rainfall increases non-linearly with increasing return period, with stronger increases in regions that already experience high rainfall.

## P95 vs P99

P99 has:

- Higher magnitude
- Wider spatial spread
- Greater sensitivity to rare extreme events

Consequently, P99 is used as the principal extreme-rainfall intensity threshold.

## Weibull vs Gumbel

Both methods produce broadly consistent spatial patterns.

However:

- Gumbel smooths extreme values through theoretical distribution fitting.
- Weibull retains greater pixel-level variability.

This supports using both methods as complementary empirical and theoretical perspectives.

## Hazard pattern

The final hazard maps show:

```text
Western coastal Maharashtra / Western Ghats
                ↓
           High hazard

Central Maharashtra
                ↓
         Moderate hazard

Eastern Maharashtra
                ↓
           Lower hazard
```

The project attributes the dominant western hazard pattern primarily to rainfall intensity, while frequency provides additional information on repeated extreme events.

---

# Validation Results

The project reports:

| Validation / Sensitivity Test | Result |
|---|---:|
| Hazard index vs 2021 P99 rainfall | Pearson r = 0.73 |
| Weight sensitivity correlations | r = 0.995–0.999 |
| Weight combinations | 0.5/0.5, 0.6/0.4, 0.7/0.3 |

The high sensitivity correlations indicate that the broad spatial hazard pattern is robust to moderate changes in the intensity/frequency weighting.

---

# Expected Outputs

The notebook generates or works with outputs including:

```text
annual_max_rainfall.nc
mean_annual_max_rainfall.tif
gumbel_return_periods.nc
gumbel_return_maps.png

district_rainfall_stats.shp
district_rainfall_stats.csv
district_all_RP_maps.png

p95_p99_maps.png
p95_p99_hist.png
p95_p99_hist_separate.png

exceedance_probability.nc
weibull_return_maps.png

hazard_index_pixel.png
district_hazard_map.png

2005_maps.png
district_2005_maps.png
district_2024_maps.png
```

Some outputs are generated directly by the notebook, while others are intermediate datasets used by later sections.

---

# How to Run

## 1. Use Google Colab

The notebook was developed for Google Colab.

Open:

```text
CM801_Group_Assignment.ipynb
```

and mount Google Drive:

```python
from google.colab import drive
drive.mount('/content/drive')
```

## 2. Install dependencies

Run:

```bash
pip install h5netcdf rioxarray geopandas rasterstats imdlib cartopy
```

## 3. Prepare the data directory

The original notebook expects:

```text
/content/drive/MyDrive/CM801/
```

with files such as:

```text
Maharashtra_Rainfall_1991_2020.nc
annual_max_rainfall.nc
gumbel_return_periods.nc
Shapefiles/
    MH.shp
    MH_Districts_SOI.shp
```

`annual_max_rainfall.nc`, `gumbel_return_periods.nc`, and other intermediate files are generated during the workflow and do not need to exist before the corresponding notebook cells are executed.

## 4. Run cells sequentially

The recommended order is:

```text
1. Install packages
2. Mount Google Drive
3. Import libraries
4. Load / prepare rainfall data
5. Calculate AMS
6. Calculate mean AMS
7. Export mean AMS raster
8. Fit Gumbel distribution
9. Calculate district Gumbel statistics
10. Calculate AMS district statistics
11. Calculate P95/P99 thresholds
12. Calculate Weibull exceedance probability
13. Compare Weibull and Gumbel
14. Calculate frequency
15. Build hazard index
16. Generate pixel-level hazard map
17. Generate district-level hazard map
18. Validate using 2021 rainfall
19. Perform sensitivity analysis
20. Run optional 2005 / 2024 analyses
```

---

# Important Notes

## Coordinate Reference System

The workflow consistently converts the vector and raster analysis layers to:

```text
EPSG:4326
```

## Latitude orientation

Several notebook sections explicitly check and correct latitude orientation before raster processing or plotting.

This is important because the rainfall NetCDF and raster-processing libraries may represent latitude in different orders.

## Missing values

Invalid or negative rainfall values are removed or masked before extreme-value calculations.

## Minimum AMS length

The Gumbel fitting step skips pixels with fewer than 10 valid annual observations.

## Pixel-wise thresholds

The hazard workflow deliberately uses spatially varying thresholds instead of one statewide threshold to account for Maharashtra's strong rainfall gradient.

---

# Limitations and Considerations

The following points are important when interpreting the workflow:

1. **Spatial resolution:** The IMD rainfall grid is approximately 27.75 km, so the hazard assessment represents relatively coarse rainfall patterns.
2. **Extreme-value sample size:** The Gumbel analysis is based on approximately 30 annual maxima, which limits the amount of information available for very long return periods.
3. **Return periods beyond the observation period:** Estimates such as 50- or 100-year rainfall are extrapolations from the fitted distribution rather than directly observed events.
4. **Hazard versus risk:** The analysis estimates rainfall hazard; it does not explicitly incorporate exposure, vulnerability, infrastructure, population, or economic loss.
5. **Fixed classification thresholds:** The district hazard classes use fixed index bins of 0–0.25, 0.25–0.5 and 0.5–1.0.
6. **Frequency definition:** Frequency depends on the selected percentile threshold, and changing that threshold can affect the resulting hazard pattern.
7. **Validation:** The reported 0.73 correlation measures spatial association with 2021 P99 rainfall; it is not a direct prediction-accuracy metric.
8. **Notebook state:** Some later notebook cells depend on variables created earlier, so cells should generally be executed sequentially.
9. **Additional analyses:** The 2005 and 2024 analyses are supplementary and should not be interpreted as replacements for the main 1991–2020 hazard framework.

---

# Future Improvements

Potential extensions include:

- Longer rainfall records for more stable extreme-value fitting
- Comparison of Gumbel with GEV and other extreme-value distributions
- Formal goodness-of-fit tests such as KS, Anderson–Darling, or likelihood-based comparison
- Cross-validation of distributional assumptions across all pixels
- More rigorous uncertainty estimation for return levels
- Inclusion of exposure and vulnerability layers
- Integration of flood susceptibility and drainage characteristics
- Higher-resolution rainfall products where appropriate
- Temporal trend analysis of extreme rainfall
- Climate-model projections for future hazard assessment
- Automated reproducible data-download and preprocessing pipelines

The notebook itself contains a note indicating that a **KS test was intended to be added**; it is therefore not presented here as an implemented part of the current workflow.

---

# Conclusion

The project demonstrates a complete spatial framework for analysing and mapping extreme rainfall hazard across Maharashtra.

The main workflow transforms 30 years of daily rainfall into annual maxima, fits a Gumbel extreme-value distribution, estimates return-period rainfall, evaluates empirical exceedance probabilities using Weibull plotting positions, derives local P95/P99 thresholds, incorporates extreme-event frequency, and combines these components into a spatial rainfall hazard index.

The results consistently identify the **Konkan coast and Western Ghats as the dominant extreme-rainfall hazard region**, with lower hazard generally occurring toward the interior and eastern parts of Maharashtra.

The reported **0.73 correlation with independent 2021 P99 rainfall** and the **0.995–0.999 sensitivity correlations** indicate that the derived hazard pattern has a strong spatial relationship with observed extreme rainfall and remains stable under moderate changes to the intensity/frequency weighting.

---

## References

The project report cites:

> Nathan, R., Jordan, P., Scorah, M., Lang, S., Kuczera, G., Schaefer, M., & Weinmann, E. (2016). Estimating the exceedance probability of extreme rainfalls up to the probable maximum precipitation. *Journal of Hydrology, 543*, 706–720.
