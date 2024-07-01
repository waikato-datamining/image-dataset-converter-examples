# Requirements

General PDF support requires the [image-dataset-converter-pdf](https://github.com/waikato-datamining/image-dataset-converter-pdf) library.


# Extract images from PDF files

The pipeline below extracts any images from the PDF files and places them in the output directory:

```bash
idc-convert \
  -l INFO \
  from-pdf \
    -l INFO \
    -i ./input/*.pdf \
    -t od \
  to-data \
    -o ./output
```

# Object detection overlay example

## Requirements

Additional image-dataset-converter library for generating the overlays: [image-dataset-converter-imgvis](https://github.com/waikato-datamining/image-dataset-converter-imgvis)

## PDF generation

The following pipeline loads predictions in OPEX format, filters out any annotations that are not of type `car` or `truck`,
generates overlays of bounding boxes with label/score and creates a PDF from these:

```bash
idc-convert \
  -l INFO \
  --force_batch \
  from-opex-od \
    -l INFO \
    -i ./predictions \
  filter-labels \
    -l INFO \
    --labels car truck \
  add-annotation-overlay-od \
    --outline_alpha 255 \
    --outline_thickness 1 \
    --fill \
    --fill_alpha 128 \
    --vary_colors \
    --font_size 10 \
    --text_format "{label}: {score}" \
    --text_placement T,L \
    --force_bbox \
  to-pdf \
    -l INFO \
    -o ./output/vehicles.pdf \
    -t --image_scale=-1
```
