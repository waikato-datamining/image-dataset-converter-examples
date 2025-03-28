Most of the time, conversion pipelines will only need to read from one
source and output to a single target. However, there can be cases where
datasets of different types need merging (*multiple inputs*) or datasets
of different types need to generated for different frameworks (*multiple outputs*).

To cater for these scenarios, the following two meta plugins are available:

* `from-multi` - reads from one or more sources using the specified readers
* `to-multi` - forwards the incoming data to the one or more writers

There is one restriction, each of the base reader/writer must be from the
same data domain.


# Multiple inputs

The following command reads a dataset in ADAMS object detection format
and MS COCO, with the combined output being saved in OPEX JSON format:

```bash
idc-convert \
  -l INFO \
  from-multi \
    -l INFO \
    -t od \
    -r "from-adams-od -l INFO -i {CWD}/input/*.report" \
       "from-coco-od -l INFO -i {CWD}/input/*.json" \
  to-opex-od \
    -l INFO \
    -o "{CWD}/output"
```

# Multiple outputs

Below, the source data is in ADAMS object detection format and will be
converted to OPEX JSON and MS COCO:

```bash
idc-convert \
  -l INFO \
  from-adams-od \
    -l INFO \
    -i {CWD}/input/*.report \
  to-multi \
    -l INFO \
    -t od \
    -w "to-opex-od -l INFO -o {CWD}/output/opex" \
       "to-coco-od -l INFO -o {CWD}/output/coco"
```
