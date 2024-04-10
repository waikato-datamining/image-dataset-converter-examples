# Video

## Requirements

Requires the [image-dataset-converter-video](https://github.com/waikato-datamining/image-dataset-converter-video) module.

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
[Redis models](https://github.com/waikato-datamining/image-dataset-converter-redis-predictions),
then it would be necessary to specify the correct type.
