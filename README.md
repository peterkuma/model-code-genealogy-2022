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

## Files

- `models.csv`: A CSV file containing a database of climate models and their
  code genealogy.
- `subset.csv`: An example subset file listing models to calculate weights for.
- `ar6_cmip5.csv`: CMIP5 models table from IPCC AR6.
- `ar6_cmip6.csv`: CMIP6 models table from IPCC AR6.
- `zelinka2021_table_S1.csv`: Table S1 from Zelinka et al. (2021).

## Programs

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

model_weights code models.csv subset.csv
```
