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
