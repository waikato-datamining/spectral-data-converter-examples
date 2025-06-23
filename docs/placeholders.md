Juggling longs paths in command-lines can be nightmare, which is the reason
the spectral-data-converter library offers support for *placeholders*. 
Placeholders can be used to shorten paths and making command-lines easier
to transfer to another environment or user. Placeholders (format `{PH}`) 
get expanded dynamically at runtime, taking the current state into account.

# Placeholder types

There are different types of placeholders:

* **System-defined** ones: 

    * `{HOME}` - the user's home directory
    * `{CWD}` - the current working directory
    * `{TMP}` - the temporary directory

* **Input-based** ones, which are based on the current input file being processed:

    * `{INPUT_PATH}` - the directory component of the current file
    * `{INPUT_NAMEEXT}` - the name (incl ext) of the current file
    * `{INPUT_NAMENOEXT}` - the name (excl ext) of the current file
    * `{INPUT_EXT}` - the extension of the current file
    * `{INPUT_PARENT_PATH}` - the path of the file's parent
    * `{INPUT_PARENT_NAME}` - the name of the file's parent

* **User-defined** ones, which are supplied to the tool itself, e.g., via the
  `-p/--placeholders` option of the `sdc-convert` tool. The same script can
  be executed using different directories when using different placeholder 
  setups. The format for the placeholders files is simple, one placeholder
  per line using `placeholder=value` as format. Empty lines and ones starting 
  with `#` get ignored.

* **Runtime** ones, which can be set with the `set-placeholder` plugin.
  These placeholders can be based on other placeholders. The reason for this
  plugin is that the output of some filters may not have any
  directory associated with them anymore, only a file name. That renders all
  the input-based placeholders unusable. Using `set-placeholder` beforehand
  allows *saving* the input directory in another placeholder for later use.
  Meta-data can be used as placeholders as well using the `metadata-to-placeholder`
  plugin, which extracts a particular key from the metadata passing through
  and updates the specified placeholder accordingly.


# Examples

## Relative to input

The following command places the converted data on the same level as the
input directory `adams` in a directory called `asc`:

```bash
sdc-convert \
  -l INFO \
  from-adams \
    -l INFO \
    -i "/some/where/adams/*.spec" \
    --labels a b c \
  to-asc \
    -l INFO \
    -o {INPUT_PARENT_PATH}/asc
```

## In-place predictions

When trying to convert spectra into another format and place them in the
same location as the input spectra, manually copying files is rather tedious.
Also, filters that get rid of the file path and only forward the file name, 
like `savitzky-golay`, invalidate the use of input-based placeholders like `{INPUT_PATH}`.
For that reason, the `set-placeholder` plugin can be used to
*back up* such placeholders in other user-defined placeholders. The following
pipeline backs up `{INPUT_PATH}` in the new placeholder `{OUTPUT_DIR}` and uses 
that for saving the generated `.asc` files:

```bash
sdc-convert -l INFO \
  from-adams \
    -i "/some/where/*.spec" \
  set-placeholder \
    -l INFO \
    -p OUTPUT_DIR \
    -v "{INPUT_PATH}" \
  savitzky-golay \
  to-asc \
    -l INFO \
    -o "{OUTPUT_DIR}"
```
