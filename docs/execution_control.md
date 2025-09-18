The following filters can be used for controlling the execution of the
pipeline:

* `block` - blocks data passing through based on a condition applied to the meta-data
* `list-to-sequence` - forwards the elements of lists individually
* `stop` - stops the pipeline if the meta-data-based condition holds true
* `sub-process` - meta-data condition determines execution of sub-filter(s)
* `tee` - meta-data condition determines forking off of data to the sub-pipeline (filter(s), [writer])
* `trigger` - meta-data condition determines execution of the sub-pipeline (reader, [filter(s)], [writer])


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
