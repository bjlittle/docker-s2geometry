<h1 align="center">
  <a href="https://s2geometry.io/" style="display: block; margin: 0 auto;">
   <img src="https://raw.githubusercontent.com/bjlittle/docker-s2geometry/master/s2geometry.gif"
        style="max-width: 10%;" alt="s2geometry"></a>
</h1>

# docker-s2geometry

[![Build Status](https://api.cirrus-ci.com/github/bjlittle/docker-s2geometry.svg)](https://cirrus-ci.com/bjlittle/docker-s2geometry)

This repository contains a recipe for building a Docker `ubuntu:bionic` base image with layers containing the `Python3.6` SWIG bindings for [google/s2geometry](https://github.com/google/s2geometry), and [SciTools/cartopy](https://github.com/SciTools/cartopy).

By default, the recipe configures an [entrypoint](https://docs.docker.com/engine/reference/builder/#entrypoint) to stand-up a [jupyter notebook](https://jupyter.org/) in the running container and will expose the service over container port `8888`.

## Build the image
Simply build the Docker image as follows:
```
> cd context
> sudo docker build -t s2geometry .
```
If necessary, override the default [ARG](https://docs.docker.com/engine/reference/builder/#arg) `port` for the `jupyter notebook`:
```
> sudo docker build -t s2geometry --build-arg port=<container-port>  .
```

## Run the container
Run the built `s2geometry` Docker image as follows:
```
> sudo docker container run -it -p 8888:8888 --rm s2geometry
```
Providing no `[COMMAND]` after the Docker `s2geometry` image will result in the `jupyter notebook` `entrypoint` executing in `--no-browser` mode.

Connect to the `jupyter notebook` running within the container on your host through your browser of choice on port `8888`.

If you want to use a different host port e.g., `9999`, then start the Docker container as follows:
```
> sudo docker container run -it -p 9999:8888 --rm s2geometry
```

To override the `entrypoint` for an interactive `bash` session in a running container, simply:
```
> sudo docker container run -it -p 8888:8888 --entrypoint "/usr/bash" --rm s2geometry
```

## Pull and run
For convenience, simply pull the `bjlittle/s2geometry` image from [Dockerhub](https://hub.docker.com/r/bjlittle/s2geometry) and run the container (in daemon-mode) as follows:
```
> sudo docker image pull bjlittle/s2geometry:latest
> sudo docker container run -d -it -p 8888:8888 --rm bjlittle/s2geometry
> firefox localhost:8888
```

## Jupyter notebook

### The default password
The `s2geometry` Docker image is configured with the `jupyter notebook` password `hello-s2!`.

See the [config/root-jupyter-notebook-config.py](https://github.com/bjlittle/docker-s2geometry/blob/master/context/config/root-jupyter-notebook-config.py#L281).

### The working directory
The default working directory for the `jupyter notebook` instance is `/root/work`, which conatains an [example notebook](https://github.com/bjlittle/docker-s2geometry/blob/master/context/notebooks/example.ipynb) to whet your appetite.

To bind mount a volume from the host into the container, simply start the container as follows:
```
> sudo docker container run -it -p 8888:8888 -v </absolute/path/to/host/dir>:/root/work/host s2geometry
```
This will allow you to create and save `jupyter notebooks` within the container into the `/root/work/host` directory, and those notebooks will then be available in your host `</absolute/path/to/host/dir>` directory.

## References
### Apps
- [Constructing the Hilbert Curve](http://bit-player.org/extras/hilbert/hilbert-construction.html)
- [Mapping points in a line to points in a square](http://bit-player.org/extras/hilbert/hilbert-mapping.html)
- [Planetary View](https://s2.sidewalklabs.com/planetaryview/)
- [Region Coverer](https://s2.sidewalklabs.com/regioncoverer/)

### Blogs
- **Nick Johnson**, [Nick's Blog](http://blog.notdot.net/) blog post: [Damn Cool Algorithms: Spatial indexing with Quadtrees and Hilbert Curves](http://blog.notdot.net/2009/11/Damn-Cool-Algorithms-Spatial-indexing-with-Quadtrees-and-Hilbert-Curves)
- **Sven Kreiss**, [Medium](https://medium.com/) blog post: [S2 cells and space-filling curves: Keys to building better digital map tools for cities](https://medium.com/sidewalk-talk/s2-cells-and-space-filling-curves-keys-to-building-better-digital-map-tools-for-cities-a312aa5e2f59)
- **Christian Peroni**, [Terra Incognita](http://blog.christianperone.com/) blog post: [Google's S2, geometry on the sphere, cells, and Hilbert curve](http://blog.christianperone.com/2015/08/googles-s2-geometry-on-the-sphere-cells-and-hilbert-curve/)
- **Antoine Sinton**, [Zenly](https://blog.zen.ly/) blog post: [Geospatial indexing on Hilbert curves](https://blog.zen.ly/geospatial-indexing-on-hilbert-curves-2379b929addc)

### Documents
- [Geometry on the Sphere: Google's S2 Library](https://docs.google.com/presentation/d/1Hl4KapfAENAOf4gv-pSngKwvS_jwNVHRPZTTDzXXn6Q/view#slide=id.i0)
- [S2 Google](https://s2geometry.io/)

### GitHub
- [beyoung/s2geometry_docker](https://github.com/beyoung/s2geometry_docker)
- [google/s2geometry](https://github.com/google/s2geometry)
- [sidewalklabs/s2sphere](https://github.com/sidewalklabs/s2sphere)

## Plans
- [x] Push a tagged version of this `s2geometry` image on Dockerhub
- [x] Build and push automatically to Dockerhub with CI 
- [ ] Provision a Docker image with default google/s2geometry SWIG Python bindings within a conda environment
  - 17 Feb 2020 - WIP
- [ ] Provision a Docker image with full API google/s2geometry Python bindings within a conda environment
  - the goal is to provision a conda package on `conda-forge` containing the Python3.x-C++ bindings for the full google/s2geometry API

Enjoy 😀
