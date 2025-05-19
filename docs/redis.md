# Requirements

Requires the [image-dataset-converter-redis](https://github.com/waikato-datamining/image-dataset-converter-redis) library.


# Object detection example

In this example we are using a prebuilt yolov5 model (using MS-COCO) to make predictions on the
frames that come from a dashcam video, overlay the predictions on the images and display them.
For the model we will be using an existing docker container.

## Requirements

**NB:** No GPU required.

Additional image-dataset-converter libraries:

* [image-dataset-converter-imgvis](https://github.com/waikato-datamining/image-dataset-converter-imgvis)
* [image-dataset-converter-video](https://github.com/waikato-datamining/image-dataset-converter-video)


## Data

### Input

![dashcam raw](img/dashcam.png)

### Output

![dashcam annotated](img/dashcam_annotated.png)

## Preparation

NB: Place all the downloads in the current directory

* Download the [dashcam01.mp4](https://github.com/lessthanoptimal/BoofCV-Data/blob/master/example/tracking/dashcam01.mp4) video from the BoofCV project
* Download the [yolo5n.pt](https://github.com/ultralytics/yolov5/releases/download/v6.0/yolov5n.pt) model
* Download the [coco.yaml](https://github.com/ultralytics/yolov5/blob/956be8e642b5c10af4a1533e09084ca32ff4f21f/data/coco.yaml) data description for the yolo5n model
* Create a directory called `cache` in the current directory
* The host machine must have a Redis server instance running. Two options:

    1. Install it natively via `sudo apt-get install redis` (and then restart it with `sudo systemctl restart redis`)
    2. Spin up a docker container with: `docker run --net=host --name redis-server -d redis`
  

## Yolov5 model

The following command launches a Yolov5 model via the container's `yolov5_predict_redis` command,
running on the CPU: 

```bash
docker run \
    --net=host -u $(id -u):$(id -g) \
    -v `pwd`:/workspace \
    -it waikatodatamining/pytorch-yolov5:2022-01-21_cpu \
  yolov5_predict_redis \
    --redis_in images \
    --redis_out predictions \
    --model /workspace/yolov5n.pt \
    --data /workspace/coco.yaml
```

## image-dataset-converter (direct prediction)

The following pipeline loads every 2nd frame from the dashcam01.mp4 video,
obtains predictions from the Yolov5 model (using the Redis backend), overlays the predictions
and then displays them:

```bash
idc-convert \
  -l INFO \
  from-video-file \
    -i ./dashcam01.mp4 \
    -n 2 \
    -t od \
  redis-predict-od \
    --channel_out images \
    --channel_in predictions \
    --timeout 1.0 \
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
  image-viewer \
    --size 800,224 \
    --delay 1
```

## image-dataset-converter (via meta-sub-images)

Like the above example, but uses the `meta-sub-images` filter to split the incoming images into two
and sends them to the model one-by-one, then assembles the predictions and forwards them:

```bash
idc-convert \
  -l INFO \
  from-video-file \
    -i ./dashcam01.mp4 \
    -n 2 \
    -t od \
  meta-sub-images \
    -l INFO \
    -r 0,0,400,224 400,0,400,224 \
    --merge_adjacent_polygons \
    --base_filter "redis-predict-od --channel_out images --channel_in predictions --timeout 1.0" \
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
  image-viewer \
    --size 800,224 \
    --delay 1
```

**NB:** 

* Requires the [image-dataset-converter-imgaug](https://github.com/waikato-datamining/image-dataset-converter-imgaug) library.
* Meta-data of merged polygons gets lost apart from the label (`type`), which has to be the same, 
  and any `score` values, which get averaged. 


# Monocular depth estimation example

This example shows how to apply the [MiDaS](https://github.com/isl-org/MiDaS)
monocular depth estimation model to images from a video and displays the 
depth information as grayscale images.

## Requirements

**NB:** No GPU required.

Additional image-dataset-converter libraries:

* [image-dataset-converter-imgvis](https://github.com/waikato-datamining/image-dataset-converter-imgvis)
* [image-dataset-converter-video](https://github.com/waikato-datamining/image-dataset-converter-video)

## Data

### Input

![dashcam raw](img/dashcam2_input.png)

### Output

![dashcam depth information as grayscale](img/dashcam2_depth.png)


## Preparation

NB: Place all the downloads in the current directory

* Download the [dashcam01.mp4](https://github.com/lessthanoptimal/BoofCV-Data/blob/master/example/tracking/dashcam01.mp4) video from the BoofCV project
* Download the [dpt_levit_224](https://github.com/isl-org/MiDaS/releases/download/v3_1/dpt_levit_224.pt) model (for more, see [here](https://github.com/waikato-datamining/monocular-depth/tree/main/midas3.1_cpu#models))
* The host machine must have a Redis server instance running. Two options:

    1. Install it natively via `sudo apt-get install redis` (and then restart it with `sudo systemctl restart redis`)
    2. Spin up a docker container with: `docker run --net=host --name redis-server -d redis`
  

## MiDaS model

The following command launches the MiDaS model via the container's `midas_predict_redis` command,
running on the CPU: 

```bash
docker run \
    --net=host -u $(id -u):$(id -g) \
    -v `pwd`:/workspace \
    -v `pwd`/cache:/.cache \
    -it waikatodatamining/midas:3.1_cpu \
  midas_predict_redis \
    --redis_in images \
    --redis_out predictions \
    --model_weights /workspace/dpt_levit_224.pt \
    --model_type dpt_levit_224 \
    --prediction_format numpy
```

## image-dataset-converter

The following pipeline loads every 2nd frame from the dashcam01.mp4 video,
tees off the input image into its own viewer, then obtains the depth 
information from the MiDaS model (using the Redis backend), turns
the predictions into grayscale images and then displays them in another
viewer window:

```bash
idc-convert \
  -l INFO \
  from-video-file \
    -i ./dashcam01.mp4 \
    -n 2 \
    -t dp \
  tee -f "image-viewer -t input -p 0,50 -s 800,224 -d 1" \
  redis-predict-dp \
    --channel_out images \
    --channel_in predictions \
    --data_format numpy \
    --timeout 1.0 \
  depth-to-grayscale \
    -t dp \
  any-to-rgb \
  image-viewer \
    -t output \
    -p 0,400 \
    -s 800,224 \
    -d 1
```
