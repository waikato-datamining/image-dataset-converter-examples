# Requirements

labelme support requires the [image-dataset-converter-labelme](https://github.com/waikato-datamining/image-dataset-converter-labelme) library.


## Image classification

The following converts all labelme `.json` files with image/label definitions 
into sub-directory format:

```bash
idc-convert -l INFO \
  from-labelme-ic \
    -l INFO \
    -i ./labelme/*.json \
  to-subdir-ic \
    -l INFO \
    -o ./subdir/
```

And this reads annotations in ADAMS .report format and stores in labelme json 
format and also specifies all possible labels:

```bash
idc-convert -l INFO \
  from-adams-ic \
    -l INFO \
    -i ./adams/*.report \
    -c classification \
  to-labelme-ic \
    -l INFO \
    --labels cat dog bunny bird \
    -o ./labelme
```

## Object detection

The following converts ADAMS object annotations to labelme `rectangle` shapes:

```bash
idc-convert -l INFO \
  from-adams-od \
    -l INFO \
    -i ./adams/*.report \
  coerce-box \
  to-labelme-od \
    -l INFO \
    -o ./labelme
```

**NB:** `coerce-box` removes any polygon annotations and only leaves the bbox.


## Instance segmentation

The following converts ADAMS object annotations to labelme `polygon` shapes:

```bash
idc-convert -l INFO \
  from-adams-od \
    -l INFO \
    -i ./adams/*.report \
  coerce-mask \
  to-labelme-od \
    -l INFO \
    -o ./labelme
```

**NB:** `coerce-mask` ensures that polygon annotations are present, even if it 
is only the outline of a bbox.
