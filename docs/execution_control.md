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


# Conditional filtering

Filtering based on meta-data is a very useful when dealing with heterogeneous 
datasets. The following pipeline fragment splits images into smaller images
only if certain conditions are met: if the image is wider than 600 pixels, then
split it into two columns; if taller than 600 pixels, then split it into
two rows.

The `dims-to-metadata` filter transfers the width/height of the current image
into its meta-data. The two `sub-process` meta-filters then execute their
sub-pipelines only if the condition evaluates to `True`. The sub-pipelines
have their own `dims-to-metadata` filter to update the meta-data with the
new dimensions of the images.


```bash
...
dims-to-metadata \
sub-process \
  -l INFO \
  --field width \
  --comparison gt \
  --value 600 \
  -f "sub-images --num_cols 2 --num_rows 1 --suffix=-y={Y},x={X},w={W},h={H} --suppress_empty dims-to-metadata" \
sub-process \
  -l INFO \
  --field height \
  --comparison gt \
  --value 600 \
  -f "sub-images --num_cols 1 --num_rows 2 --suffix=-y={Y},x={X},w={W},h={H} --suppress_empty dims-to-metadata" \
...
```


# Sub-pipeline file format

`sub-process`, `tee` and `trigger` define their sub-flow either explicitly
as a single command-line or point to a file with the sequence of plugins. The
former should be used for simple sub-flows, whereas the latter makes it easier
to develop more complex ones.

The file format is quite simple:

* plain text with extension of your choice
* line comments start with `#`
* blank lines get ignored
* no line continuation, like `\` or `^`, is required

After stripping the file of blank and comment lines (white spaces at start/end 
of lines get removed as well), all the plugins and their arguments get 
concatenated using spaces. The single command-line is then parsed.

Of course, further sub-pipelines can be defined in such a file. There is no
limit to nesting, as they get expanded/processed as they are encountered.
