FROM ubuntu:bionic

LABEL author="bjlittle" \
      version="0.1" \
      description="Docker image for Google s2geometry and Python3.x SWIG bindings"

ENV BASE_S2_DIR="/root/s2geometry" \
    WORK_DIR="/root/work" \
    JUPYTER_CONFIG="/root/.jupyter/jupyter_notebook_config.py"

#
# install system-level dependencies
#
RUN apt update -y \
  && apt upgrade -y \
  && apt install -y build-essential \
                    cmake \
                    git \
                    libgflags-dev \
                    libgeos++-dev \
                    libgoogle-glog-dev \
                    libgtest-dev \
                    libproj-dev \
                    libssl-dev \
                    swig \
                    pkg-config \
                    python3 \
                    python3-dev \
                    python3-distutils-extra \
                    python3-pip \
                    vim

# create convenience link for python to default to python3 (obviously)
RUN ln -sf /usr/bin/python3 /usr/bin/python

#
# build, test and install google s2geometry (c++) with SWIG python bindings
#
RUN git clone https://github.com/google/s2geometry.git ${BASE_S2_DIR}

WORKDIR ${BASE_S2_DIR}/build

RUN cmake -D GTEST_ROOT=/usr/src/gtest \
          -D WITH_GFLAGS=ON \
          -D WITH_GLOG=ON \
          -D PYTHON_LIBRARY:FILEPATH=$(python -c "from distutils.sysconfig import get_config_vars; d=get_config_vars(); print('/usr/lib/{}/{}'.format(d['MULTIARCH'], d['INSTSONAME']))") \
          -D PYTHON_INCLUDE_DIR:PATH=$(python -c "from distutils.sysconfig import get_python_inc; print(get_python_inc())") \
          -D PYTHON_EXECUTABLE:FILEPATH=$(which python) \
          -D CMAKE_INSTALL_PREFIX=/usr \
          -D BUILD_SHARED_LIBS=ON \
          ..

RUN make \
  && ctest -j4 \
  && make install

# create convenience "s2" module link for SWIG python bindings to s2geometry
RUN DIST_PACKAGES_DIR=$(python -c "from distutils.sysconfig import get_python_lib; print(get_python_lib())") \
  && ln -s ${DIST_PACKAGES_DIR}/pywraps2.py ${DIST_PACKAGES_DIR}/s2.py

#  
# PyPI install cartopy and friends...
#
RUN pip3 install --upgrade pip
# reference: https://github.com/Toblerity/Shapely/issues/566
RUN pip3 install --no-binary Shapely Shapely
RUN python -m pip install jupyter ipywidgets matplotlib pillow scipy pyepsg OWSLib cartopy

#
# XXX: workaround required for cartopy master (pre-0.18.0 release)
#
ENV BASE_DIR_CARTOPY="/root/cartopy"
RUN git clone https://github.com/SciTools/cartopy.git ${BASE_DIR_CARTOPY}
WORKDIR ${BASE_DIR_CARTOPY}
RUN python -m pip install Cython
RUN python -m pip install -e .

# setup jupyter with prior configuration
COPY ./config/root-jupyter-notebook-config.py ${JUPYTER_CONFIG}

# include the example jupyter notebook
WORKDIR ${WORK_DIR}
COPY ./notebooks/example.ipynb ${WORK_DIR}

# override default port with "docker build --build-arg port=<port>"
ARG port=8888
EXPOSE ${port}
RUN sed -i "s/^\(c\.NotebookApp\.port = \)8888$/\1${port}/" ${JUPYTER_CONFIG}

# start jupyter notebook as a service, by default...
ENTRYPOINT ["jupyter", "notebook"]
