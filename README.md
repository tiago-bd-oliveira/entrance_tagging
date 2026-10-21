# Entrance Detection

This project is to detect the main entrance of publich buildings based only on the available geospatial data on OpenStreetMap (OSM).

## Install

The scripts require Python >= 3.14 and [uv](https://docs.astral.sh/uv/). They mainly use
[imbalanced-learn](https://imbalanced-learn.readthedocs.io/en/stable/index.html) and
[scikit-learn](https://scikit-learn.org/stable/).

```bash
cd entrance_tagging
uv sync
```

## Usage

All scripts read and write under `data/` by default; pass `--data-dir <dir>` to use another directory.

### Data Preprocessing

Put the raw feature file in `data/` (default name `osmfeatures-test25.csv`, override with
`--raw-file`). The original data of the paper is
[osmfeatures-test25.xls](https://drive.google.com/file/d/1lVG3zhWKvR1SVVN3QgLGAyxmc0c97QXJ/view?usp=sharing),
which must be exported to CSV first.

The file has one block per building: a header row
`building_id, entrance_index, entrance_x, entrance_y, n_samples`, followed by `n_samples` rows
`row_len, dist_to_next_sample, x, y, name1, value1, name2, value2, ...`. A feature that is missing for a
sample is left out of its row and imputed later.

Split the buildings into five cross-validation groups (writes `data/<g>/test_list.csv`), then build each
group's training and test sets. Missing data is imputed, and only strong negative samples are kept
(`--pt` and `--ft` set the physical and feature distance thresholds):

```bash
uv run split_groups.py
uv run extraction.py
```

### Training and Predicting

Train the weighted random forest (WRF) and balanced random forest (BRF), the two best models in the
paper, and report the mean distance error over the test buildings (writes
`data/tagging_result_<model>.csv`):

```bash
uv run training_tagging.py
```

`uv run prediction_test.py` runs the same evaluation and plots the cumulative distribution of the
distance error (`--no-show` saves it to `data/error_cdf.png` instead).

## Citation

missing now

## License

missing now
