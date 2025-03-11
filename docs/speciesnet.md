# Requirements

Requires the following libraries: 
 
* [image-dataset-converter-redis](https://github.com/waikato-datamining/image-dataset-converter-redis)
* [image-dataset-converter-video](https://github.com/waikato-datamining/image-dataset-converter-video)

Either one of the SpeciesNet Docker images:

* [CUDA](https://github.com/waikato-datamining/speciesnet/tree/main/4.0.1_cuda12.1)
* [CPU](https://github.com/waikato-datamining/speciesnet/tree/main/4.0.1_cpu)

**NB:** If you haven't used Docker before, then have a look at 
[this tutorial](https://www.data-mining.co.nz/docker-for-data-scientists/).

The following directory structure below your current directory:

```
  .
  |
  +- cache
  |
  +- config
  |
  +- input
  |
  +- output 
```

With `input` containing one or more video files in AVI format,
and `output` for storing the predictions in [OPEX format](https://github.com/waikato-datamining/fast-opex).

**NB:** MP4 files can be processed as well. 


## SpeciesNet model

Start the SpeciesNet model in Redis mode:

* CUDA
    
```bash
docker run --rm --gpus=all --shm-size 8G --net=host \
  -u $(id -u):$(id -g) -e USER=$USER \
  -v `pwd`:/workspace \
  -v `pwd`/cache:/.cache \
  -v `pwd`/config:/.config \
  -v `pwd`/cache:/.torch \
  -it waikatodatamining/speciesnet:4.0.1_cuda12.1
  speciesnet_predict_redis \
    --redis_in images \
    --redis_out predictions \
    --verbose
```

* CPU
     
```bash
docker run --rm --shm-size 8G --net=host \
  -u $(id -u):$(id -g) -e USER=$USER \
  -v `pwd`:/workspace \
  -v `pwd`/cache:/.cache \
  -v `pwd`/config:/.config \
  -v `pwd`/cache:/.torch \
  -it waikatodatamining/speciesnet:4.0.1_cpu \
  speciesnet_predict_redis \
    --redis_in images \
    --redis_out predictions \
    --verbose
```


## Processing single video file

### Determine changes between frames

If you want to skip similar frames, e.g., leaves moving in the wind, it pays 
to look at the frame changes in a video using the `calc-frame-changes` filter.
However, if you are looking for small animals like rodents, you may need to 
process all frames to avoid missing these.

The following command processes the file `VID0003.AVI` and calculates the
changes between frames as a ratio (0-1) with a minimum change of `0.000001`
required:

```bash
idc-convert -l INFO \
   from-video-file \
     -l INFO \
     -i "./input/VID0003.AVI" \
     -t od \
   calc-frame-changes \
     -t 0.000001
```

This will output a histogram similar to this:

```
+1.09e-06 - +3.22e-06  [57]  ████████████████████████████████████████
+3.22e-06 - +5.35e-06  [29]  ████████████████████▍
+5.35e-06 - +7.49e-06  [13]  █████████▏
+7.49e-06 - +9.62e-06  [14]  █████████▉
+9.62e-06 - +1.18e-05  [15]  ██████████▌
+1.18e-05 - +1.39e-05  [10]  ███████
+1.39e-05 - +1.60e-05  [11]  ███████▊
+1.60e-05 - +1.82e-05  [ 7]  ████▉
+1.82e-05 - +2.03e-05  [ 3]  ██▏
+2.03e-05 - +2.24e-05  [ 2]  █▍
+2.24e-05 - +2.46e-05  [ 2]  █▍
+2.46e-05 - +2.67e-05  [ 2]  █▍
+2.67e-05 - +2.88e-05  [ 1]  ▊
+2.88e-05 - +3.10e-05  [ 1]  ▊
+3.10e-05 - +3.31e-05  [ 5]  ███▌
+3.31e-05 - +3.52e-05  [ 0]
+3.52e-05 - +3.74e-05  [ 0]
+3.74e-05 - +3.95e-05  [ 0]
+3.95e-05 - +4.16e-05  [ 2]  █▍
+4.16e-05 - +4.38e-05  [ 1]  ▊
```

Using the threshold `5.35e-06`, you will skip frames that would fall into the 
top two bins of the above histogram.

### Extracting frames

With the threshold for our video determined, we can now extract relevant 
frames and push them through the SpeciesNet model.

The following command processes `VID0003.AVI` from the `input` directory (`from-video-file`),
determines frames that differ enough from each other (`skip-similar-frames`) 
to send to the model (`redis-predict-od`), removes any predictions with a score 
lower than 0.8 (`metadata-od`), discards any results from the model
with no predictions (`discard-negatives`) 
and stores any images with predictions in the `output` directory in 
[OPEX format](https://github.com/waikato-datamining/fast-opex):

```bash
idc-convert -l INFO \
  from-video-file \
    -l INFO \
    -i "./input/VID0003.AVI" \
    -t od \
  skip-similar-frames \
    -l INFO \
    -t 5.35e-06 \
  redis-predict-od \
    -l INFO \
    -o images \
    -i predictions \
    -t 10 \
  metadata-od \
    -l INFO \
    -f score \
    -c gt \
    -v 0.8 \
    -a keep \
  discard-negatives \
    -l INFO \
  to-opex-od \
    -l INFO \
    -o ./output
```


## Processing multiple video files

The `from-video-file` reader is not limited to just processing a single file,
e.g., you can supply multiple file names to the `-i` option or use a *glob* 
like `*.AVI`. However, with different videos having different lighting and
therefore different changes between frames, it makes more sense returning 
only every nth frame to reduce the processing time. You can either use `-n/--nth_frame`
regardless of the frame-rate of the video or `-f/--fps_factor` which calculates
the actual nth frame to return based on the frame-rate of the video multiplied by
this factor. The command below processes all `.AVI` files from the `input`
directory and retrieves one frame for every second of video footage (`-f 1`):

```bash
idc-convert -l INFO \
  from-video-file \
    -l INFO \
    -i "./input/*.AVI" \
    -f 1 \
    -t od \
  redis-predict-od \
    -l INFO \
    -o images \
    -i predictions \
    -t 10 \
  metadata-od \
    -l INFO \
    -f score \
    -c gt \
    -v 0.8 \
    -a keep \
  discard-negatives \
    -l INFO \
  to-opex-od \
    -l INFO \
    -o ./output
```
