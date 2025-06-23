Below are examples for using the spectral-data-converter library via its 
[Docker images](https://github.com/waikato-datamining/spectral-data-converter-all/tree/main/docker).


# Interactive session

The following command starts an interactive session, mapping the current working
directory to `/workspace`:

```bash
docker run --rm -u $(id -u):$(id -g) \
    -v `pwd`:/workspace \
    -it waikatodatamining/spectral-data-converter:latest
```

# Conversion pipeline

The following converts an spectra in [ADAMS format](https://github.com/waikato-datamining/spectral-data-converter/blob/main/formats/adams.md) 
to [ASC](https://github.com/waikato-datamining/spectral-data-converter/blob/main/formats/asc.md) format:

```bash
docker run --rm -u $(id -u):$(id -g) \
    -v `pwd`:/workspace \
    -it waikatodatamining/spectral-data-converter:latest \
    sdc-convert -l INFO \
      from-adams \
        -l INFO \
        -i /workspace/input/*.spec \
      to-asc \
        -l INFO \
        -o /workspace/output
```

**NB:** The `input` and `output` directories are located below the current working directory (`pwd`).
