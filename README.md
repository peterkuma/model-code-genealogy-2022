# Model code genealogy

This repository contains code to calculate climate model averaging weights
based on multiple choices of model "democracy":

- `variant`: All model variants are equal.
- `model`: All models are equal.
- `institute`: All institutes are equal.
- `country`: All countries are equal.
- `code`: Model weights are calculated based on model code genealogy.

## Requirements

- Python 3
- numpy
- pandas

## Input files

Input files are stored in the `input` directory.

- `models.csv`: A CSV file containing a database of climate models and their
  code genealogy.
- `subset.csv`: An example subset file listing models to calculate weights for.
- `ar6_cmip5.csv`: CMIP5 models table from IPCC AR6.
- `ar6_cmip6.csv`: CMIP6 models table from IPCC AR6.
- `zelinka2021_table_S1.csv`: Table S1 from Zelinka et al. (2021).
- `zelinka2021_table_S2.csv`: Table S2 from Zelinka et al. (2021).

## Programs

Programs are stored in the `bin` directory.

### model\_weights

```
Calculate model weights based on code genealogy.

Usage: model_weights <type> <models> [<subset>]

Arguments:

- type: Type of "democracy". One of: "variant", "model", "institution", "country", "code".
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

bin/plot_feedbacks input/models.csv input/zelinka2021_table_S{2,1}.csv plot/feedbacks.pdf
```
