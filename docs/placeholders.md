Juggling longs paths in command-lines can be nightmare, which is the reason
the image-dataset-converter library offers support for *placeholders*. 
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
  `-p/--placeholders` option of the `idc-convert` tool. The same script can
  be executed using different directories when using different placeholder 
  setups. The format for the placeholders files is simple, one placeholder
  per line using `placeholder=value` as format. Empty lines and ones starting 
  with `#` get ignored.

* **Runtime** ones, which can be set with the `set-placeholder` plugin.
  These placeholders can be based on other placeholders. The reason for this
  plugin is that the output of filters like `meta-sub-images` don't have any
  directory associated with them anymore, only a file name. That renders all
  the input-based placeholders unusable. Using `set-placeholder` beforehand
  allows *saving* the input directory in another placeholder for later use.
  Meta-data can be used as placeholders as well using the `metadata-to-placeholder`
  plugin, which extracts a particular key from the metadata passing through
  and updates the specified placeholder accordingly.


# Examples

## Relative to input

The following command places the converted data on the same level as the
input directory `indexed` in a directory called `grayscale`:

```bash
idc-convert \
  -l INFO \
  from-indexed-png-is \
    -l INFO \
    -i "/some/where/indexed/*.png" \
    --labels a b c \
  to-grayscale-is \
    -l INFO \
    -o {INPUT_PARENT_PATH}/grayscale
```

## In-place predictions

When trying to add predictions from a model to a directory structure of images,
it can be tedious copying images around. Instead, if the model can communicate
via Redis, the predictions can be placed in the same location as the input
file even if the filter like `meta-sub-images` should invalidate the input-based
placeholders like `{INPUT_PATH}`. The following command saves the current
input directory in `{OUTPUT_DIR}`, which is then used to save the generated
prediction:

```bash
idc-convert -l INFO \
  from-data \
    -t is \
    -i "/some/where/**/*.jpg" \
  set-placeholder \
    -l INFO \
    -p OUTPUT_DIR \
    -v "{INPUT_PATH}" \
  meta-sub-images \
    -l INFO \
    --num_rows 2 \
    --num_cols 2 \
    -b "redis-predict-is -l INFO --labels a b c -o image -i prediction -t 10" \
  discard-negatives \
  to-indexed-png-is \
    -l INFO \
    -o "{OUTPUT_DIR}" \
    --annotations_only
```
