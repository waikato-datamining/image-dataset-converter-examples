# Docker

Below are examples for using the image-dataset-converter library via its 
[Docker images](https://github.com/waikato-datamining/image-dataset-converter/tree/main/docker).


## Interactive session

The following command starts an interactive session, mapping the current working
directory to `/workspace`:

```bash
docker run --rm -u $(id -u):$(id -g) \
    -v `pwd`:/workspace \
    -it waikatodatamining/image-dataset-converter:latest
```
