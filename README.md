# Model code genealogy

This repository contains code to accompanying the manuscript "Climate model
code genealogy and its relation to climate feedbacks and sensitivity".

The code contains programs for calculating climate model averaging weights
based on multiple choices of model "democracy":

- `variant`: All model runs (here also called "variants") are equal.
- `model`: All models are equal.
- `institute`: All institutes are equal.
- `country`: All countries are equal.
- `code`: Model weights are calculated based on model code genealogy.

## Requirements

The programs are to be run on Linux or another unix-like operating system in
the Bash shell.

System requirements:

- Python 3.9
- NetCDF4 (tested with 4.7.4)

On Debian-based Linux distributions, these can be installed with:

```sh
apt install python3 libnetcdf-dev
```

Required Python packages:

- numpy 1.22.1
- scipy 1.7.3
- pandas 1.4.3
- pymc3 3.11.5
- ds-format 2.0.0

The Python packages can be installed with

```sh
pip3 install -r requirements.txt
```

## Input files

Input files are stored in the `input` directory.

- `models.csv`: A CSV file containing a database of climate models and their
  code genealogy.
- `subset.csv`: An example subset file listing models to calculate weights for.
- `ar6_cmip5.csv`: CMIP5 models table from IPCC AR6.
- `ar6_cmip6.csv`: CMIP6 models table from IPCC AR6.
- `zelinka2021_table_S1.csv`: Table S1 from Zelinka et al. (2021).
- `zelinka2021_table_S2.csv`: Table S2 from Zelinka et al. (2021).
- `cmip5.tar.xz`: CMIP5 temperature data.
- `cmip6.tar.xz`: CMIP6 temperature data.
- `CMIP5_ECS_ERF_fbks.csv`: CMIP5 feedback, forcing and ECS data from [1].
- `CMIP5_ECS_ERF_fbks.txt`: The same as the above but in the original format.
- `CMIP6_ECS_ERF_fbks.csv`: CMIP6 feedback, forcing and ECS data from [1].
- `CMIP6_ECS_ERF_fbks.txt`: The same as the above but in the original format.

\[1\]: https://github.com/mzelinka/cmip56_forcing_feedback_ecs

Before using the scripts, the input files need to be prepared by unpacking
the archives `cmip5.tar.xz` and `cmip6.tar.xz`.

## Programs

Programs are stored in the `bin` directory.

### model\_weights

```
Calculate model weights based on code genealogy.

Usage: model_weights <type> <models> [<subset>]

Arguments:

- type: Type of "democracy". One of: "variant", "model", "institute", "country", "code", "family".
- models: Table of all models (CSV).
- subset: List of models to calculate weights for (CSV).

Files:

The models CSV file should be "models.csv", supplied with the code. The optional
subset CSV file should contains a single column "Model" with a list of models,
referring to a model or a variant in the "Model" or "CMIPx names" columns
of the models file, respectively.

Example:

bin/model_weights code input/models.csv input/subset.csv > data/model_weights_code.csv
```

### calc\_model\_stats

```
Calculate model statistics.

Usage: calc_model_stats <input> <models>

Arguments:

- input: Input data (CSV). The table should contain a column "Model" identifying
the model, and an arbitrary number of other columns to calculate statistics for.
- models: Table of all models (CSV).

Examples:

bin/calc_model_stats input/zelinka2021_table_S1.csv input/models.csv > data/model_stats_zelinka2021_table_S1.csv
bin/calc_model_stats input/ar6_cmip5.csv input/models.csv > data/model_stats_ar6_cmip5.csv
bin/calc_model_stats input/ar6_cmip6.csv input/models.csv > data/model_stats_ar6_cmip6.csv
```

### plot\_feedbacks

```
Plot bar plot with model feedbacks.

Usage: plot_feedbacks <models> <cmip5> <cmip6> <output>

Arguments:

- models: Table of all models (CSV).
- cmip5, cmip6: Input data (CSV). The table should contain a column "Model"
identifying the model, and columns to calculate statistics for.
- output: Output plot (PDF).

Examples:

bin/plot_feedbacks input/models.csv input/CMIP{5,6}_ECS_ERF_fbks.csv plot/feedbacks.pdf
```

### plot\_feedbacks\_by\_group

```
Plot bar plot with model feedbacks by model group (family or country).

Usage: plot_feedbacks_by_group <group> <models> <cmip5> <cmip6> <output> [<output_cmip5> <output_cmip6> [<bayes_cmip5> <bayes_cmip6 ]]

Arguments:

- group: Group to show. One of: "family", "country".
- sep: CMIP5 and CMIP6 separate or together. One of "separate", "together".
- models: Table of all models (CSV).
- cmip5, cmip6: Input data (CSV). The table should contain a column "Model"
identifying the model, and columns to calculate statistics for.
- output: Output plot (PDF).
- output_cmip5: CMIP5 plot data output (NetCDF).
- output_cmip6: CMIP6 plot data output (NetCDF).
- bayes_cmip5: CMIP5 Bayes test input (NetCDF).
- bayes_cmip6: CMIP5 Bayes test input (NetCDF).

Examples:

bin/plot_feedbacks_by_group family separate input/models.csv input/CMIP{5,6}_ECS_ERF_fbks.csv plot/feedbacks_by_family.pdf
bin/plot_feedbacks_by_group family separate input/models.csv input/CMIP{5,6}_ECS_ERF_fbks.csv plot/feedbacks_by_family.pdf data/feedbacks_by_family_cmip{5,6}.nc
bin/plot_feedbacks_by_group family separate input/models.csv input/CMIP{5,6}_ECS_ERF_fbks.csv plot/feedbacks_by_family.pdf data/feedbacks_by_family_cmip{5,6}.nc data/feedbacks_by_family_cmip{5,6}_bayes.nc
```

### calc\_bayes

```sh
Calculate statistical significance in feedback by group difference between the overall mean and the group mean.

Usage: calc_bayes <input> <output>

Arguments:

- input: Input file. The output of plot_feedbacks_by_group (NetCDF).
- output: Output file (NetCDF).

Examples:

bin/calc_bayes data/feedbacks_by_family_cmip5.nc data/feedbacks_by_family_cmip5_bayes.nc
bin/calc_bayes data/feedbacks_by_family_cmip6.nc data/feedbacks_by_family_cmip6_bayes.nc
```

### plot\_tas

```sh
Plot global mean near-surface air temperature.

Usage: plot_tas <models> <control> <tas> <hadcrut> <output> <y1> <y2> <title> divider: <divider>

Arguments:

- models: Table of models (CSV).
- control: Input directory with model tas - piControl or none (NetCDF).
- tas: Input directory with model tas - experiment (NetCDF).
- hadcrut: Input HadCRUT file (NetCDF).
- output: Output plot (PDF).
- y1: Start year.
- y2: End year.
- title: Plot title.

Options:

- divider: Year to plot vertical divider on.

Example:

bin/plot_tas input/models.csv none input/cmip6/historical/tas input/HadCRUT.5.0.1.0.analysis.summary_series.global.monthly.nc plot/tas_cmip6_historical.pdf 1850 2014 'CMIP6 historical'
bin/plot_tas input/models.csv none input/cmip6/historical+ssp245/tas input/HadCRUT.5.0.1.0.analysis.summary_series.global.monthly.nc plot/tas_cmip6_historical+ssp245.pdf 1850 2099 'CMIP6 historical + SSP2-4.5' divider: 2015
bin/plot_tas input/models.csv input/cmip6/{piControl,abrupt-4xCO2}/tas none plot/tas_cmip6_abrupt-4xCO2.pdf 1 150 'CMIP6 abrupt-4xCO2'
bin/plot_tas input/models.csv input/cmip6/{piControl,1pctCO2}/tas none plot/tas_cmip6_1pctCO2.pdf 1 150 'CMIP6 1pctCO2'
bin/plot_tas input/models.csv none input/cmip5/historical/tas input/HadCRUT.5.0.1.0.analysis.summary_series.global.monthly.nc plot/tas_cmip5_historical.pdf 1850 2005 'CMIP5 historical'
bin/plot_tas input/models.csv none input/cmip5/historical+rcp45/tas input/HadCRUT.5.0.1.0.analysis.summary_series.global.monthly.nc plot/tas_cmip5_historical+rcp45.pdf 1850 2099 'CMIP5 historical + RCP4.5' divider: 2006
bin/plot_tas input/models.csv input/cmip5/{piControl,abrupt-4xCO2}/tas none plot/tas_cmip5_abrupt-4xCO2.pdf 1 140 'CMIP5 abrupt-4xCO2'
bin/plot_tas input/models.csv input/cmip5/{piControl,1pctCO2}/tas none plot/tas_cmip5_1pctCO2.pdf 1 140 'CMIP5 1pctCO2'
```
