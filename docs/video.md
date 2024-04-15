# Requirements

Requires the [image-dataset-converter-video](https://github.com/waikato-datamining/image-dataset-converter-video) library.

# Plugins

## Extract frames from video file

The following extracts a total of 100 frames from `./input/video.mp4`, using every 
20th frame and stores the images in the `./output` directory: 

```bash
idc-convert \
  -l INFO \
  from-video-file \
    -l INFO \
    -i ./input/video.mp4 \
    -t od \
    -n 20 \
    -m 100 \
  to-data \
    -l INFO \
    -o ./output/
```

Though the data type is *object detection* (`-t od`), this is not really relevant in this
example. If there were other filters, e.g., for applying object detection 
[Redis models](redis-predictions.md),
then it would be necessary to specify the correct type.


## Capture frames from a webcam (individual JPG files)

The following captures a total of 10 images from a webcam (using every 20th frame) and stores
them as individual JPG files in the `./output` directory:

```bash
idc-convert \
  -l INFO \
  from-webcam \
    -l INFO \
    -i 0 \
    -t od \
    -n 20 \
    -m 10 \
  to-data \
    -l INFO \
    -o ./output
```


## Capture frames from a webcam (store as MJPEG file)

The following captures a total of 100 images from a webcam (using every 10th frame) and generates a MJPEG file,
which can be viewed with, e.g., [ffplay](https://ffmpeg.org/ffplay.html):

```bash
idc-convert \
  -l INFO \
  from-webcam \
    -l INFO \
    -i 0 \
    -t od \
    -n 10 \
    -m 100 \
  to-video-file \
    -l INFO \
    -o ./output/webcam.mjpeg
```

## Calculating differences between frames

Depending on the scene, frames may not differ much between each other, making it hard to extract frames
by using specific intervals. The `calc-frame-changes` writer computes differences between frames and
output a histogram of these differences. Using the min/max of the bins of the histogram, it is possible
to find a suitable *change* threshold. This threshold can be utilized by the `skip-similar-frames` filter
to discard too similar frames, whittling down the number of extracted frames automatically.
The histogram can be output in textual form, CSV or JSON; either on stdout or stored in a file.

```bash
idc-convert \
  -l INFO \
  from-video-file \
    -l INFO \
    -i ./input/video.mp4 \
    -t od \
    -n 10 \
    -m 2000 \
  calc-frame-changes \
    -f text
```

## Skipping similar frames

The following command keeps frames that differ enough from each other and discards all others, 
using the `skip-similar-frames` filter. The threshold applied by the filter was previously
determined by the `calc-frame-changes` filter.


```bash
idc-convert \
  -l INFO \
  from-video-file \
    -l INFO \
    -i ./input/video.mp4 \
    -t od \
  skip-similar-frames \
    -t 0.005 \
  to-data \
    -l INFO \
    -o ./output
```

## Youtube video

The `from-youtube` reader allows you to grab frames from a Youtube video:

```bash
idc-convert \
  -l INFO \
  from-youtube \
    -l INFO \
    -i "https://www.youtube.com/watch?v=CFdZWgiAj8I" \
    -p the_cloud-kitty_flanagan- \
    -t od \
    -n 20 \
    -m 10 \
  to-data \
    -l INFO \
    -o ./output
```

**NB:** Please make sure not to infringe on any copyright.


## Youtube live stream

The `from-youtube-live` reader allows you to grab frames from a Youtube live stream, e.g., wildlife cameras:

```bash
idc-convert \
  -l INFO \
  from-youtube-live \
    -l INFO \
    -i "https://www.youtube.com/watch?v=2swy9gysvOY" \
    -p african_river_wildlife_camera- \
    -t od \
    -n 20 \
    -m 10 \
  to-data \
    -l INFO \
    -o ./output
```

**NB:** Please make sure not to infringe on any copyright.
