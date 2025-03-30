The following sections only show snippets of commands, as there are quite a number of filters available.

## Bounding box / polygon

With the `coerce-bbox` filter, you can force annotations to be bounding box only.
The reverse is the `coerce-mask` filter, which ensures that all annotations are available as polygons.


## Too small or too large?

Using the `dimension-discarder` filter, you can filter out too large or too small images quite easily:

* only allow within certain width/height constraint

```bash
...
dimension-discarder \
  -l INFO \
  --min_height 100 \
  --max_height 200 \
  --min_width 100 \
  --max_width 200 \
...
```

* only a certain area, but the shape is irrelevant

```bash
...
dimension-discarder \
  -l INFO \
  --min_area 10000 \
  --max_area 50000 \
...
```

## Domain conversion

* *object detection to image classification:* With the `od-to-ic` filter you can convert object detection 
  annotations to image classification. How multiple differing labels are handled can be specified.
* *object detection to image segmentation:* The `od-to-is` filter generates image segmentation data 
  from the bbox/polygon annotations.


## Annotation management

* `filter-labels` - leaves only the matching labels in the annotations
* `map-labels` - for renaming labels
* `remove-classes` - removes the specified labels
* `strip-annotations` - removes all annotations
* `write-labels` - outputs a list of all the encountered labels


## Meta-data management

* `metadata` - allows comparisons on meta-data values and whether to keep or discard a record in case of a match
* `metadata-from-name` - allows extraction of meta-data value from the image name via a regular expression
* `split` - adds the field `split` to the meta-data of the record passing through, which can be acted on with other filters (or stored in the output)


## Record management

A number of generic record management filters are available:

* `check-duplicate-filenames` - when using multiple batches as input, duplicate file names can be an issue when creating a combined output
* `discard-invalid-images` - attempts to load the image and discards them in case the loading fails (useful when data acquisition can generate invalid images)
* `discard-negatives` - removes records from the stream that have no annotations
* `max-records` - limits the number of records passing through
* `randomize-records` - when processing batches, this filter can randomize them (seeded or unseeded)
* `record-window` - only lets a certain window of records pass through (e.g., the first 1000)
* `rename` - allows renaming of images, e.g., prefixing them with a batch number/ID
* `sample` - for selecting a random sub-sample from the stream
