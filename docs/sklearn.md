# Requirements

Requires the [spectral-data-converter-sklearn](https://github.com/waikato-datamining/spectral-data-converter-sklearn) library.


# Plugins

## Training a model  

The following command-line loads spectra in ADAMS format, leaves only
every 4th wave number, then builds a PLS regression model with three 
components on the `al.ext_usda.a1056_mg.kg` target using `sklearn-fit` 
and saves it to disk: 

```bash
sdc-convert -l INFO -b \
  from-adams \
    -l INFO \
    -i {CWD}/train/*.spec \
  downsample \
    -n 4 \
  sklearn-fit \
    -l INFO \
    -m sklearn.cross_decomposition.PLSRegression \
    -p "{\"n_components\": 3}" \
    -t al.ext_usda.a1056_mg.kg \
    -o {CWD}/model/al.pkl
```


## Making predictions

Having a trained model in place, we can use it to make predictions. The
`sklearn-predict` filter loads a pickled model from disk and then generates
a prediction for each spectrum passing through, setting the value under
the specified target sample data field:

```bash
sdc-convert -l INFO -b \
  from-adams \
    -l INFO \
    -i {CWD}/test/*.spec \
  downsample \
    -n 4 
  sklearn-predict \
    -l INFO \
    -m {CWD}/model/al.pkl \
    -t al.ext_usda.a1056_mg.kg \
  to-adams \
    -l INFO \
    -o {CWD}/predictions \
    --output_sampledata 
```
