# Requirements

Paddle support requires the [image-dataset-converter-paddle](https://github.com/waikato-datamining/image-dataset-converter-paddle) library.


## Image classification

The following converts all Paddle `.txt` files with image/label definitions 
into sub-directory format:

```bash
idc-convert -l INFO \
  from-paddle-ic \
    -l INFO \
    -i ./paddle/*.txt \
    -m ./paddle/label_list.map \
  to-subdir-ic \
    -l INFO \
    -o ./subdir/
```
