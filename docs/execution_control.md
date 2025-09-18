The following filters can be used for controlling the execution of the
pipeline:

* `block` - blocks data passing through based on a condition applied to the meta-data
* `list-to-sequence` - forwards the elements of lists individually
* `stop` - stops the pipeline if the meta-data-based condition holds true
* `sub-process` - meta-data condition determines execution of sub-filter(s)
* `tee` - meta-data condition determines forking off of data to the sub-pipeline (filter(s), [writer])
* `trigger` - meta-data condition determines execution of the sub-pipeline (reader, [filter(s)], [writer])


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
