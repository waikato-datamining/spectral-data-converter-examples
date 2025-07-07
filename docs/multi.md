Most of the time, conversion pipelines will only need to read from one
source and output to a single target. However, there can be cases where
data of different types need merging (*multiple inputs*) or data source
of different types need to be generated for different frameworks (*multiple outputs*).

To cater for these scenarios, the following two meta plugins are available:

* `from-multi` - reads from one or more sources using the specified readers
* `to-multi` - forwards the incoming data to one or more writers

There is one restriction, each of the base reader/writer must be from the
same data domain.


# Multiple inputs

## Spectra

The following command reads data in Opus and ASCII XY, with the combined 
output being saved in ADAMS format:

```bash
sdc-convert \
  -l INFO \
  from-multi \
    -l INFO \
    -r "from-opus -l INFO -i {CWD}/input/opus/*.0" \
       "from-asciixy -l INFO -i {CWD}/input/asciixy/*.txt" \
  to-adams \
    -l INFO \
    -o "{CWD}/output"
```

## Sample data

The following reads sample data in CSV and JSON format and outputs the records
in ADAMS format:

```bash
sdc-convert \
  -l INFO \
  from-multi \
    -l INFO \
    -r "from-csv-sd -l INFO -i {CWD}/csv/input/*.csv" \
       "from-json-sd -l INFO -i {CWD}/json/input/*.json" \
  to-report-sd \
    -l INFO \
    -o {CWD}/output/
```


# Multiple outputs

## Spectra

Below, the source data is in ADAMS format and will be
converted to ASC and ASCII XY:

```bash
sdc-convert \
  -l INFO \
  from-adams \
    -l INFO \
    -i {CWD}/input/*.spec \
  to-multi \
    -l INFO \
    -w "to-asc -l INFO -o {CWD}/output/asc" \
       "to-asciixy -l INFO -o {CWD}/output/asciixy"
```

## Sample data

The following loads ADAMS sample data records and saves them in CSV
and JSON format:

```bash
sdc-convert \
  -l INFO \
  from-report-sd \
    -l INFO \
    -i {CWD}/adams/input/*.report \
  to-multi \
    -l INFO \
    -w "to-csv-sd -l INFO -o {CWD}/output/{INPUT_NAMENOEXT}.csv" \
       "to-json-sd -l INFO -o {CWD}/output/"
```

**NB:** Since `to-csv-sd` is a batch writer expecting an output file rather 
than a directory, we have to resort to using the `{INPUT_NAMENOEXT}`
placeholder to generate output.


# Sub-pipelines

With the `tee` meta-filter, it is possible to filter the spectra coming through with a separate
sub-pipeline. That allows converting the incoming data into multiple output formats with
their own preprocessing.

The following command loads the ADAMS annotations and saves them in ASC and ASCII XY format
in one command, but one with centered and the other with log-transformed data:

```bash
sdc-convert \
  -l INFO \
  -b \
  from-adams \
    -l INFO \
    -i "./adams/*.spec" \
  tee \
    -f "center to-asc -l INFO -o ./tee-asc/" \
  tee \
    -f "standardize to-asciixy -l INFO -o ./tee-asciixy/"
```
