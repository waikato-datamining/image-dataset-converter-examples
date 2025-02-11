# Requirements

Paddle support requires the [image-dataset-converter-paddle](https://github.com/waikato-datamining/image-dataset-converter-paddle) library.


## Image classification

With the following command, a sub-dir structured is converted into Paddle
format. The integer ID/label text mapping is stored in `./paddle/labels.map`,
the label ID/file mapping in `files.txt` and the images are stored relatively
to the output directory in the `jpg` sub-directory:

```bash
idc-convert -l INFO \
  from-subdir-ic \
    -l INFO \
    -i ./subdir \
  to-paddle-ic \
    -l INFO \
    -o ./paddle \
    --id_label_map ./paddle/labels.map \
    --file_label_map files.txt \
    --relative_path jpg
```

The following converts all Paddle `.txt` files with image/label definitions 
back into sub-directory format:

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

## Image segmentation

The following converts indexed PNGs into Paddle's image segmentation format:

```bash
idc-convert -l INFO \
  from-indexed-png-is \
    -l INFO \
    -i ./indexed/*.png \
    --labels l1 l2 l3 l4 \
  to-paddle-is \
    -l INFO \
    -o ./paddle
```

And this command converts the Paddle format into indexed PNGs again:

```bash
idc-convert -l INFO \
  from-paddle-is \
    -l INFO \
    -i ./paddle/*_list.txt \
    --labels_file ./paddle/labels.txt \
  to-indexed-png-is \
    -l INFO \
    -o ./indexed
```
