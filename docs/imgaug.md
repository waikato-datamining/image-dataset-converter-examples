# Requirements

Requires the [image-dataset-converter-imgaug](https://github.com/waikato-datamining/image-dataset-converter-imgaug) library.

Download the [17 flowers](https://datasets.cms.waikato.ac.nz/ufdl/17flowers/) 
*object detection* VOC XML dataset and extract it.

# Plugins

## Convert VOC XML to YOLO (crop/rotate)

The following converts VOC XML annotations into YOLO ones. It also augments the
dataset with randomly adding cropped/rotated images to the stream (`-m add`),
with the annotations getting processed accordingly:

```bash
idc-convert \
  -l INFO \
  from-voc-od \
    -l INFO \
    -i "./voc/*.xml" \
  crop \
    -m add \
    -f 0.1 \
    -t 0.2 \
    -u \
  rotate \
    -m add \
    -f "-45" \
    -t 45 \
  to-yolo-od \
    -l INFO \
    -o ./yolo-split-augmented \
    --labels ./yolo-split-augmented/labels.txt \
    --split_names train val test \
    --split_ratios 70 15 15
```

Here is an example of a processed image (image_0001.jpg):

![Example image cropped and rotated](img/image_0001-cropped-rotated.jpg)


## Convert VOC XML to MS COCO (resize)

The following converts VOC XML annotations into smaller MS COCO ones, using a maximum 
width of 300 while keeping the aspect ratio intact. The data also gets split into 
train/validation/test:

```bash
idc-convert \
  -l INFO \
  from-voc-od \
    -l INFO \
    -i "./voc/*.xml" \
  resize \
    -W 300 \
    -H keep-aspect-ratio \
  to-coco-od \
    -l INFO \
    -o ./coco-split-small \
    --split_names train val test \
    --split_ratios 70 15 15
```


## Split images into smaller ones

With the `sub-images` filter it is possible to split images into smaller ones or extract
just specific regions of interest from images. The `idc-generate-regions` tool can be used
to generate regions for a set number of rows/columns or fixed row heights/column widths.

For images that are at maximum 800x800, we can generate regions for a 2x2 grid as follows: 

```bash
idc-generate-regions \
  -l INFO \
  -W 800 \
  -H 800 \
  -r 2 \
  -c 2
```

Will output this, with each quadruplet consisting of x, y, width and height:

```
0,0,400,400 400,0,400,400 0,400,400,400 400,400,400,400
```

These regions we can now use with the `sub-images` filter to generate smaller images:

```bash
idc-convert \
  -l INFO \
  from-voc-od \
    -l INFO \
    -i "./voc/*.xml" \
  sub-images \
    -l INFO \
    -r 0,0,400,400 400,0,400,400 0,400,400,400 400,400,400,400 \
    -p \
    -e \
  to-yolo-od \
    -l INFO \
    -o ./yolo-sub \
    --labels ./yolo-sub/labels.txt
```

Using `-p` we will keep partial annotations, ones that got cut off a bit, and with `-e` we 
will suppress images that have no annotations in them.

Using the `meta-sub-images` filter, you can feed the small images that were generated
through the specified base filter. The output of this filter gets reassembled and forwarded. 
This base filter can be a simple filter or a more complex pipeline, which passes the 
images through a [Redis model](redis.md), for instance.
