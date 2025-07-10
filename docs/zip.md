# Requirements

ZIP file support comes as part of the core library (spectral-data-converter).

The `from-zip` meta-reader and `to-zip` meta-writer can use any reader/writer
as base reader/writer that implements the `DirectReader`/`DirectWriter` mixin, 
as they can handle reading from/writing to file-like objects. That applies to
spectra as well as sample data readers/writers.

**Notes**

* The `to-zip` writer is a stream writer, which means that spectra will
  get stored separately as individual files in the archive. This happens 
  regardless of whether batches of data arrive.
* When specifying the reader/writer in the command-line, you need to use 
  the `--option="argument"` notion. When using `--option "argument"`, the 
  reader/writer in the argument will get interpreted as part of the 
  pipeline.


# Plugins

## Reading

The following command reads spectra in ADAMS .spec format from a zip file, 
down-samples them (i.e., only leaves every nth wave number) and then saves them
to an output directory:

```bash
sdc-convert -l INFO \
  from-zip \
    -l INFO \
    -i "./input/spec.zip" \
    -p "*.spec" \
    -r="from-adams -l INFO" \
  downsample \
    -n 4 \
  to-adams \
    -l INFO \
    -o ./output
```

Sample data files can be read in the same way:

```bash
sdc-convert -l INFO \
  from-zip \
    -l INFO \
    -i "./input/sampledata.zip" \
    -p "*.report" \
    -r="from-report-sd -l INFO" \
  to-json-sd \
    -l INFO \
    -o ./output
```


## Writing

The command below reads spectra in ADAMS .spec format, applies `row-norm` (aka 
standard-normal-variate) to them and then stores them in DPT format in the zip 
file:

```bash
sdc-convert -l INFO \
  from-adams \
    -l INFO \
    -i "./input/*.spec" \
  row-norm \
  to-zip \
    -l INFO \
    -o ./output/dpt.zip \
    -w="to-dpt -l INFO"
```

We can also extract the sample data information from spectra and store just 
that in a zip file:

```bash
sdc-convert -l INFO \
  from-adams \
    -l INFO \
    -i "./input/*.spec" \
  spectrum-to-sampledata \
  to-zip \
    -l INFO \
    -o ./output/sampledata.zip \
    -w="to-json-sd -l INFO"
```
