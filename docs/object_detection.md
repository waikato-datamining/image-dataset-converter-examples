Readers and writers for object detection have the `-od` suffix.

Download the [17 flowers](https://datasets.cms.waikato.ac.nz/ufdl/17flowers/) 
*object detection* VOC XML dataset and extract it.

# VOC XML to MS COCO

The following converts the VOC XML dataset into MS COCO format, a format used by frameworks 
like [Detectron2](https://github.com/facebookresearch/detectron2) or 
[MMDetection](https://github.com/open-mmlab/mmdetection/):

```bash
idc-convert \
  -l INFO \
  from-voc-od \
    -l INFO \
    -i ./voc/*.xml \
  to-coco-od \
    -l INFO \
    -o ./coco
```

# VOC XML to MS COCO (train/val/test splits)

You can also split the data, e.g., into train, validation and test subsets:

```bash
idc-convert \
  -l INFO \
  from-voc-od \
    -l INFO \
    -i ./voc/*.xml \
  to-coco-od \
    -l INFO \
    -o ./coco-split \
    --split_names train val test \
    --split_ratios 70 15 15
```

**NB:** The subsets will be placed into sub-directories according to the split name.
