No library can dream of offering all the required functionality. Especially for one-off tasks, it makes no sense to 
develop a whole new plugin library. Hence, there are the following generic plugins that allow the user to utilize 
custom Python functions:

* reader: `from-pyfunc` - takes a single string as input and outputs an iterable of spectrum containers
* filter: `pyfunc-filter` - takes a single spectrum container or an iterable of them as input and outputs a single container or an iterable of them
* writer: `to-pyfunc` - processes a single spectrum container or an iterable of them and an optional split name

In order to use such a custom function, they must be specified in the following format (option: `-f/--function`):

```
module_name:function_name
```

If the code below were available through module `my.code`, then the function specifications would be as follows:

* reader: `my.code:pyfunc_reader`
* filter: `my.code:pyfunc_filter`
* writer: `my.code:pyfunc_writer`


```python
from typing import Iterable
from kasperl.api import make_list, flatten_list
from sdc.api import Spectrum, Spectrum2D
from wai.spectralio.adams import read

# reader: generates spectrum containers from the path in ADAMS format   
def pyfunc_reader(path: str) -> Iterable[Spectrum2D]:
    for sp in read(path, options=["--keep-format"]):
        yield Spectrum2D(source=path, spectrum=sp)

# filter: simply adds a note to the meta-data
def pyfunc_filter(data):
    result = []
    for item in make_list(data):
        if not item.has_metadata():
            meta = dict()
        else:
            meta = item.get_metadata()
        meta["note"] = "filtered by a python function!"
        item.set_metadata(meta)
        result.append(item)
    return flatten_list(result)

# writer: simply outputs name and meta-data and, if present, also the split
def pyfunc_writer(data: Spectrum, split: str = None):
    if split is None:
        print("name: ", data.spectrum_name, ", meta:", data.get_metadata())
    else:
        print("split:", split, ", name:", data.spectrum_name, ", meta:", data.get_metadata())
```
