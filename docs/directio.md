The spectral-data-converter library is mainly designed to be used as 
a command-line pipeline for working with files. However, you can also
use any of the readers/writers that implement the `seppl.io.DirectReader` / 
`seppl.io.DirectWriter` mixins. By being able to handle file-like objects,
you can move away from just using files via paths.

Though the examples below are still using files for reading/writing, the
concept is the same if you want to access other types of data.

# Spectra

The following code snippet reads an SPA file and writes the spectra in
ADAMS format to another directory:

```python
from sdc.reader import SPAReader as Reader
from sdc.writer import AdamsWriter as Writer
from seppl import Session

r = Reader(direct_read=True)
r.initialize()
r.session = Session()
w = Writer(output_sampledata=True)
w.initialize()
w.session = r.session

f_in = "./input/cylohex.spa"
with open(f_in, "rb") as fp:
    sps = [x for x in r.read_fp(fp)]

f_out = "./output/"
for i, sp in enumerate(sps):
    with open(f_out + str(i) + ".spec", "w") as fp:
        w.write_stream_fp(sp, fp, False)
```


# Sample data

The following code snippet reads sample data records from a CSV file and 
writes them in JSON format to another directory:

```python
from sdc.reader import CSVSampleDataReader as Reader
from sdc.writer import JsonSampleDataWriter as Writer
from seppl import Session

r = Reader(direct_read=True, sample_id="1", sample_data="2-last")
r.initialize()
r.session = Session()
w = Writer()
w.initialize()
w.session = r.session

f_in = "./input/sampledata.csv"
with open(f_in, "r") as fp:
    sds = [x for x in r.read_fp(fp)]

f_out = "./output/"
for i, sd in enumerate(sds):
    with open(f_out + str(i) + ".json", "w") as fp:
        w.write_stream_fp(sd, fp, False)
```
