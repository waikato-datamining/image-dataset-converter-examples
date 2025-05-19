# Requirements

Requires the [image-dataset-converter-redis](https://github.com/waikato-datamining/image-dataset-converter-redis) library.

## Plugins

The following plugins can be used for generating domain specific predictions:

* `redis-predict-dp` - generate predictions using a depth estimation model, 
  e.g., like [these ones](https://github.com/waikato-datamining/monocular-depth)
* `redis-predict-ic` - classify images, e.g., using 
  [MMClassification](https://www.data-mining.co.nz/applied-deep-learning/image_classification/mmclassification/) 
  or [PaddleClas](https://www.data-mining.co.nz/applied-deep-learning/image_classification/paddleclas/)
* `redis-predict-is` - perform image segmentation, e.g., 
  using [MMSegmentation](https://www.data-mining.co.nz/applied-deep-learning/image_segmentation/mmsegmentation/)
  or [PaddleSeg](https://www.data-mining.co.nz/applied-deep-learning/image_segmentation/paddleseg/)
* `redis-predict-od` - for applying object detection and instance segmentation, e.g., 
  [MMDetection](https://www.data-mining.co.nz/applied-deep-learning/object_detection/mmdetection/),
  [PaddleDetection](https://www.data-mining.co.nz/applied-deep-learning/object_detection/paddledetection/),
  various YOLO variants ([Yolov5](https://www.data-mining.co.nz/applied-deep-learning/object_detection/yolov5/),
  [Yolov7](https://www.data-mining.co.nz/applied-deep-learning/object_detection/yolov7/), 
  [Yolov10](https://www.data-mining.co.nz/applied-deep-learning/object_detection/yolov10/))
  or [Detectron2](https://www.data-mining.co.nz/applied-deep-learning/instance_segmentation/detectron2/)

## Practical examples

The following articles describe how image-dataset-converter and 
Docker images can be integrated into pipelines for processing data:

* [Depth estimation in videos](depth_estimation_in_videos.md)
* [Object detection in videos](object_detection_in_videos.md)
