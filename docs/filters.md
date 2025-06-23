The following sections only show snippets of commands, as there are quite a number of filters available.

## Spectral filters

* `center` - subtracts the column mean from the columns (batch filter)
* `downsample` - extracts every n-th wave number
* `equi-distance` - evenly spaces the wave numbers
* `log` - log-transforms the amplitudes
* `pca` - applies principal components analysis for dimensionality reduction
* `pls1` - applies the PLS1 partial-least-squares algorithm (batch filter)
* `rownorm` (aka `standard-normal-variate`) - subtracts mean and divides by standard deviation
* `savitzky-golay` and `savitzky-golay2` - the Savitzky-Golay smoothing algorithm
* `simpls` - applies the SIMPLS partial-least-squares algorithm (batch filter)
* `standardize` - column-wise subtracts the column mean and divides by the column stdev (batch filter)

Applying PLS1:

```bash
sdc-convert -l INFO -b \
  from-adams \
    -l INFO \
    -i {CWD}/input/*.spec \
  pls1 \
    -l INFO \
    -n 4 \
    -r al.ext_usda.a1056_mg.kg \
  to-adams \
    -l INFO \
    -o {CWD}/output/ \
    --output_sampledata  
```

Using Savitzky-Golay:

```bash
sdc-convert -l INFO \
  from-adams \
    -l INFO \
    -i {CWD}/input/*.spec \
  savitzky-golay \
    -l INFO \
    -L 3 \
    -R 5 \
  to-adams \
    -l INFO \
    -o {CWD}/output/ \
    --output_sampledata
```


## Meta-data management

* `metadata` - allows comparisons on meta-data values and whether to keep or discard a record in case of a match
* `metadata-from-name` - allows extraction of meta-data value from the spectrum name via a regular expression
* `metadata-to-placeholder` - turns meta-data into placeholders, which can be used for redirecting output of writers
* `split-records` - adds the field `split` to the meta-data of the record passing through, which can be acted on with other filters (or stored in the output)

Splitting data into train/test and training `center` on the `train` batch:

```bash
sdc-convert -l INFO -b \
  from-adams \
    -l INFO \
    -i {CWD}/input/*.spec \
  split-records \
    --split_names train test \
    --split_ratios 50 50 \
  center \
    -l INFO \
    -k split \
    --batch_order train test \
  to-adams \
    -l INFO \
    -o {CWD}/output/ \
    --output_sampledata
```

## Record management

A number of generic record management filters are available:

* `check-duplicate-filenames` - when using multiple batches as input, duplicate file names can be an issue when creating a combined output
* `discard-by-name` - discards spectra based on their name, either using explicit names or regular expressions, e.g., excluding quality control samples
* `max-records` - limits the number of records passing through
* `randomize-records` - when processing batches, this filter can randomize them (seeded or unseeded)
* `record-window` - only lets a certain window of records pass through (e.g., the first 1000)
* `rename` - allows renaming of spectra, e.g., prefixing them with a batch number/ID
* `sample` - for selecting a random sub-sample from the stream

Randomizing records and outputting the first 100:

```bash
sdc-convert -l INFO \
  from-adams \
    -l INFO \
    -i {CWD}/input/*.spec \
  randomize-records \
    -l INFO \
    -s 1 \
  max-records \
    -l INFO \
    -m 100 \
  to-adams \
    -l INFO \
    -o {CWD}/test/adams/output/ \
    --output_sampledata
```
