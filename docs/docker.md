Below are examples for using the image-dataset-converter library via its 
[Docker images](https://github.com/waikato-datamining/image-dataset-converter-all/tree/main/docker).


# Interactive session

The following command starts an interactive session, mapping the current working
directory to `/workspace`:

```bash
docker run --rm -u $(id -u):$(id -g) \
    -v `pwd`:/workspace \
    -it waikatodatamining/image-dataset-converter:latest
```

# Conversion pipeline

The following converts an image classification dataset from the sub-dir format
(sub-directory names represent the image classification labels) into the 
[ADAMS format](https://github.com/waikato-datamining/image-dataset-converter/blob/main/formats/adams.md), 
which stores the label in an associated .report file (Java properties file):

```bash
docker run --rm -u $(id -u):$(id -g) \
    -v `pwd`:/workspace \
    -it waikatodatamining/image-dataset-converter:latest \
    idc-convert -l INFO \
      from-subdir-ic \
        -l INFO \
        -i /workspace/input/ \
      to-adams-ic \
        -l INFO \
        -o /workspace/output \
        -c classification
```

**NB:** The `input` and `output` directories are located below the current working directory (`pwd`).
