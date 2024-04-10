Readers and writers for image segmentation have the `-ic` suffix.

Download the [17 flowers](https://datasets.cms.waikato.ac.nz/ufdl/17flowers/) 
*image classification* dataset and extract it.

# sub-dir to ADAMS

The following converts an image classification dataset from the sub-dir format
(sub-directory names represent the image classification labels) into the 
[ADAMS format](https://github.com/waikato-datamining/image-dataset-converter/blob/main/formats/adams.md), 
which stores the label in an associated .report file (Java properties file):

```bash
idc-convert -l INFO \
  from-subdir-ic \
    -l INFO \
    -i ./subdir/ \
  to-adams-ic \
    -l INFO \
    -o ./adams \
    -c classification
```


# sub-dir (randomized train/val/test splits)

By enforcing batch-processing `--force_batch` and using the 
`randomize-records` filter, randomized train/val/test splits
(writers typically support generating splits) can be generated 
like this:

```bash
idc-convert -l INFO --force_batch \
  from-subdir-ic \
    -l INFO \
    -i ./subdir/ \
  randomize-records \
    -s 42 \
  to-subdir-ic \
    -l INFO \
    -o ./adams-split \
    --split_names train val test \
    --split_ratios 70 15 15
```
