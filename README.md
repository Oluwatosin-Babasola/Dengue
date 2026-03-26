# Climate-Driven Spatiotemporal Modeling of Dengue in Mexico (2020–2025)

## Project Overview

This repository contains code and data processing workflows for a municipality-level spatiotemporal analysis of confirmed dengue transmission in Mexico from 2020 to 2025. The primary objective is to quantify how climate variability explains observed geographic and seasonal heterogeneity in dengue burden while accounting for spatial dependence and population structure.

The analysis integrates epidemiological surveillance data, climate variables, and official population statistics within a Bayesian spatial modeling framework implemented in Python using PyMC.

The study is data-driven, operates at weekly temporal resolution, and incorporates explicit spatial structure via intrinsic conditional autoregressive modeling.

---

## Scientific Objective

To estimate the climate-associated spatiotemporal dynamics of confirmed dengue burden across Mexican municipalities from 2020 to 2025, while:

1. Quantifying spatial clustering
2. Estimating climate effects on per capita dengue risk
3. Identifying municipalities with residual burden after climate adjustment
4. Evaluating model adequacy and spatial smoothing behavior

The work emphasize on  inference rather than short-term prediction.

---

## Data Sources

### 1. Dengue Surveillance Data

Source
Dirección General de Epidemiología (DGE), Mexico
Open national microdata

Variables used

* FECHA_SIGN_SINTOMAS (date of symptom onset)
* ESTATUS_CASO (confirmed case indicator)
* DEFUNCION (death indicator)
* ENTIDAD_RES (state code)
* MUNICIPIO_RES (municipality code)

Processing steps

* Restricted to confirmed cases where ESTATUS_CASO equals 1
* Parsed dates and converted to ISO epidemiological year and week
* Constructed municipality code as 5-digit CVEGEO equivalent
* Aggregated to municipality–week panel

Temporal coverage
2020 through 2025
Year 2026 excluded due to incomplete reporting.

---

### 2. Municipality Boundaries

Source
Instituto Nacional de Estadística y Geografía (INEGI)
Marco Geoestadístico Nacional
Municipality shapefile (Área Geoestadística Municipal)

Used for

* Spatial mapping
* Construction of adjacency matrix
* ICAR spatial random effects

---

### 3. Population Data

Source
INEGI Catálogo Único de Claves Geoestadísticas
Municipality-level total population, 2020 census

Variable used

* pob_total

Used to construct population offset in the negative binomial model.

The offset allows modeling of per capita dengue risk while retaining count-based likelihood.

---

### 4. Climate Data

Temperature and Humidity
Source: NASA POWER Agroclimatology database
Variables:

* T2M (2 meter air temperature)
* RH2M (2 meter relative humidity)

Precipitation
Source: CHIRPS v2.0 daily rainfall data

Processing

* Daily data aggregated to ISO week
* Municipality centroids used to extract grid cell values
* Weekly mean temperature
* Weekly cumulative rainfall
* Weekly mean relative humidity

---

## Unit of Analysis

Municipality–week panel:

muni_code, iso_year, iso_week

Outcome variable

* Weekly confirmed dengue cases

Covariates

* Weekly mean temperature
* Weekly cumulative rainfall
* Weekly mean relative humidity

Population

* Municipality total population 2020

---

## Modeling Framework

### Likelihood

Negative binomial model for overdispersed count data.

cases_it ~ NegBin(mu_it, dispersion)

### Linear predictor

log(mu_it) = log(pop_i)
+ intercept
+ beta × standardized climate
+ year random effect
+ spatial ICAR effect

Where:

* log(pop_i) is a population offset
* beta represents climate-associated multiplicative change in per capita risk
* ICAR captures spatial autocorrelation
* Year effect captures interannual variability

Inference performed using variational inference via ADVI in PyMC.

---

## Figures Generated

Figure 1
Annual dengue burden maps (2020–2025)

Figure 2
Cumulative dengue burden map (2020–2025)

Figure 3
National weekly time series

Figure 4
Spatial autocorrelation assessment

Figure 5
Hotspot maps

Figure 6
Climate-adjusted residual burden (log scale)

Figure 7
Exceedance probability maps

Figure 8
Posterior climate effects on per capita dengue risk

Figure 10
Observed versus fitted counts for pooled 2020–2025 model

---


## Environment

All modeling and preprocessing performed in:

Conda environment: chirps_netcdf
Python version: 3.11
Key libraries:

* pandas
* geopandas
* numpy
* matplotlib
* pymc
* arviz
* xarray
* netCDF4

To activate:

conda activate chirps_netcdf
jupyter notebook


