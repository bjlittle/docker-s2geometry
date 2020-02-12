# docker-s2geometry

<h1 align="center">
  <a href="https://s2geometry.io/" style="display: block; margin: 0 auto;">
   <img src="https://raw.githubusercontent.com/bjlittle/docker-s2geometry/master/s2geometry.png"
        style="max-width: 40%;" alt="s2geometry"></a>
</h1>

This repository contains the recipe for building a Docker ubuntu:bionic base image containing the Python3 SWIG bindings for [google/s2geometry](https://github.com/google/s2geometry) and [SciTools/cartopy](https://github.com/SciTools/cartopy).

By default, the recipe configures an [entrypoint](https://docs.docker.com/engine/reference/builder/#entrypoint) to stand-up a [jupyter notebook](https://jupyter.org/) in the running container and will expose the service over container port `8888`.

## Build the image
Simply build the Docker image as follows:
```
> sudo docker build -t s2geometry .
```
Override the default [ARG](https://docs.docker.com/engine/reference/builder/#arg) `port` for the `jupyter notebook`:
```
> sudo docker build -t s2geometry --build-arg port=<port>  .
```

## Run the container
Run the built `s2geometry` Docker image as follows:
```
> sudo docker container run -it -p 8888:8888 --rm s2geometry
```
Providing no `[COMMAND]` after the Docker `s2geometry` image will result in the `jupyter notebook` entrypoint executing in `--no-browser` mode.

Connect to the `jupyter notebook` running in the container through your browser of choice on port `8888`.

To override the `entrypoint` for an interactive `bash` session in the running container, simply:
```
> sudo docker container run -it -p 8888:8888 --entrypoint "/usr/bash" --rm s2geometry
```

## Jupyter notebook password
The `s2geometry` Docker image is configured with the `jupyter notebook` password `hello-s2!`.

See the [config/root-jupyter-nbtebook-config.py](https://github.com/bjlittle/docker-s2geometry/blob/master/config/root-jupyter-notebook-config.py#L281).
