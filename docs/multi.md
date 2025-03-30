Most of the time, conversion pipelines will only need to read from one
source and output to a single target. However, there can be cases where
datasets of different types need merging (*multiple inputs*) or datasets
of different types need to generated for different frameworks (*multiple outputs*).

To cater for these scenarios, the following two meta plugins are available:

* `from-multi` - reads from one or more sources using the specified readers
* `to-multi` - forwards the incoming data to one or more writers

There is one restriction, each of the base reader/writer must be from the
same data domain.


# Multiple inputs

The following command reads a dataset in [ADAMS object detection](https://github.com/waikato-datamining/image-dataset-converter/blob/main/formats/adams.md) format
and [MS COCO](https://cocodataset.org/#format-data), with the combined output being saved in [OPEX JSON](https://github.com/WaikatoLink2020/objdet-predictions-exchange-format) format:

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

Below, the source data is in [ADAMS object detection](https://github.com/waikato-datamining/image-dataset-converter/blob/main/formats/adams.md) format and will be
converted to [OPEX JSON](https://github.com/WaikatoLink2020/objdet-predictions-exchange-format) 
and [MS COCO](https://cocodataset.org/#format-data):

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


# Sub-pipelines

With the `tee` meta-filter, it is possible to filter the images coming through with a separate
sub-pipeline. That allows converting the incoming data into multiple output formats with
their own preprocessing.

The following command loads the VOC XML annotations and saves them in ADAMS and YOLO format
in one command, but each scaled differently:

```bash
idc-convert \
  -l INFO \
  from-voc-od \
    -l INFO \
    -i "./voc/*.xml" \
  tee \
    -f "scale -m replace -T 0 -f 0.5 -t 0.5 -u to-adams-od -o ./adams-tee/" \
  tee \
    -f "scale -m replace -T 0 -f 0.25 -t 0.25 -u to-yolo-od -o ./yolo-tee/ --labels ./yolo-tee/labels.txt"
```
