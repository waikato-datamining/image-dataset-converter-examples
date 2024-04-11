# Requirements

Requires the [image-dataset-converter-imgstats](https://github.com/waikato-datamining/image-dataset-converter-imgstats) library.

Download the [17 flowers](https://datasets.cms.waikato.ac.nz/ufdl/17flowers/) 
*object detection* VOC XML dataset and extract it.

# Plugins

## Label distribution

Using the `label-dist` writer, you can output a distribution of the labels within
a dataset as follows:

```bash
idc-convert \
  -l INFO \
  from-voc-od \
    -l INFO \
    -i "./voc/*.xml" \
  label-dist \
    -l INFO
```

This will output the following:

```
Bluebell: 28
Buttercup: 54
ColtsFoot: 55
Crocus: 50
Daffodil: 71
Daisy: 57
Dandelion: 43
Fritillary: 65
Iris: 77
LilyValley: 17
Pansy: 56
Snowdrop: 50
Sunflower: 71
Tigerlily: 50
Tulip: 41
Windflower: 63
```

**NB:** You can also generate CSV or JSON output.

## Area histogram

Knowing the distribution of the extends of your annotations can be quite useful, e.g., 
for determining whether down-sampling can be an option. The `area-histogram` writer
generates histograms for all objects combined and one for each category:

```bash
idc-convert \
  -l INFO \
  from-voc-od \
    -l INFO \
    -i "./voc/*.xml" \
  area-histogram \
    -l INFO
```

The output for the 17 flowers dataset looks like this:

```
ALL:

+1.26e+04 - +3.29e+04  [ 14]  █████▎
+3.29e+04 - +5.33e+04  [ 30]  ███████████▍
+5.33e+04 - +7.36e+04  [ 36]  █████████████▋
+7.36e+04 - +9.40e+04  [ 54]  ████████████████████▍
+9.40e+04 - +1.14e+05  [ 71]  ██████████████████████████▊
+1.14e+05 - +1.35e+05  [ 71]  ██████████████████████████▊
+1.35e+05 - +1.55e+05  [ 95]  ███████████████████████████████████▉
+1.55e+05 - +1.75e+05  [104]  ███████████████████████████████████████▎
+1.75e+05 - +1.96e+05  [ 94]  ███████████████████████████████████▌
+1.96e+05 - +2.16e+05  [106]  ████████████████████████████████████████
+2.16e+05 - +2.37e+05  [ 77]  █████████████████████████████
+2.37e+05 - +2.57e+05  [ 48]  ██████████████████▏
+2.57e+05 - +2.77e+05  [ 14]  █████▎
+2.77e+05 - +2.98e+05  [ 10]  ███▊
+2.98e+05 - +3.18e+05  [  9]  ███▍
+3.18e+05 - +3.38e+05  [  6]  ██▎
+3.38e+05 - +3.59e+05  [  2]  ▊
+3.59e+05 - +3.79e+05  [  5]  █▉
+3.79e+05 - +3.99e+05  [  1]  ▍
+3.99e+05 - +4.20e+05  [  1]  ▍

Bluebell:

+7.68e+04 - +9.18e+04  [1]  ████████
+9.18e+04 - +1.07e+05  [1]  ████████
+1.07e+05 - +1.22e+05  [2]  ████████████████
+1.22e+05 - +1.37e+05  [0]
+1.37e+05 - +1.51e+05  [0]
+1.51e+05 - +1.66e+05  [2]  ████████████████
+1.66e+05 - +1.81e+05  [2]  ████████████████
+1.81e+05 - +1.96e+05  [0]
+1.96e+05 - +2.11e+05  [5]  ████████████████████████████████████████
+2.11e+05 - +2.26e+05  [1]  ████████
+2.26e+05 - +2.41e+05  [4]  ████████████████████████████████
+2.41e+05 - +2.56e+05  [3]  ████████████████████████
+2.56e+05 - +2.71e+05  [1]  ████████
+2.71e+05 - +2.86e+05  [0]
+2.86e+05 - +3.01e+05  [2]  ████████████████
+3.01e+05 - +3.16e+05  [0]
+3.16e+05 - +3.31e+05  [1]  ████████
+3.31e+05 - +3.46e+05  [0]
+3.46e+05 - +3.61e+05  [0]
+3.61e+05 - +3.76e+05  [3]  ████████████████████████

Buttercup:
...
```


**NB:** You can also generate CSV or JSON output.
