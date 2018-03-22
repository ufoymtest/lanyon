---
layout: post
title: Introducing Deepo
excerpt_separator: ---
---

Given the current state of deep learning research and development, we may need to play around with different frameworks (e.g., PyTorch for research and fun, Caffe2 for edge device inference, etc). However, setting up all the deep learning frameworks to coexist and function correctly is tedious and time-consuming.

So I made ***Deepo***, which is a series of [*Docker*](http://www.docker.com/) images that
- allows you to quickly set up your deep learning research environment
- supports almost all [commonly used deep learning frameworks](#tags)
- supports [GPU acceleration](#GPU) (CUDA and cuDNN included), also works in [CPU-only mode](#CPU)
- works on Linux ([CPU version](#CPU)/[GPU version](#GPU)), Windows ([CPU version](#CPU)) and OS X ([CPU version](#CPU))

and their Dockerfile generator that
- allows you to [customize your own environment](#Build) with Lego-like modules
- automatically resolves the dependencies for you

---

# Table of contents
- [Quick Start](#Quick-Start)
  - [GPU Version](#GPU)
    - [Installation](#Installation)
    - [Usage](#Usage)
  - [CPU Version](#CPU)
    - [Installation](#Installation-cpu)
    - [Usage](#Usage-cpu)
- [Customization](#Customization)
  - [Unhappy with all-in-one solution?](#One)
  - [Other python versions](#Python)
  - [Jupyter support](#Jupyter)
  - [Build your own customized image](#Build)
- [Contributing](#Contributing)

---

<a name="Quick-Start"/>

# Quick Start


<a name="GPU"/>

## GPU Version

<a name="Installation"/>

### Installation

#### Step 1. Install [Docker](https://docs.docker.com/engine/installation/) and [nvidia-docker](https://github.com/NVIDIA/nvidia-docker).

#### Step 2. Obtain the all-in-one image from [Docker Hub](https://hub.docker.com/r/ufoym/deepo)

```bash
docker pull ufoym/deepo
```

<a name="Usage"/>

### Usage

Now you can try this command:
```bash
nvidia-docker run --rm ufoym/deepo nvidia-smi
```
This should work and enables Deepo to use the GPU from inside a docker container.
If this does not work, search [the issues section on the nvidia-docker GitHub](https://github.com/NVIDIA/nvidia-docker/issues) -- many solutions are already documented. To get an interactive shell to a container that will not be automatically deleted after you exit do

```bash
nvidia-docker run -it ufoym/deepo bash
```

If you want to share your data and configurations between the host (your machine or VM) and the container in which you are using Deepo, use the -v option, e.g.
```bash
nvidia-docker run -it -v /host/data:/data -v /host/config:/config ufoym/deepo bash
```
This will make `/host/data` from the host visible as `/data` in the container, and `/host/config` as `/config`. Such isolation reduces the chances of your containerized experiments overwriting or using wrong data.

Please note that some frameworks (e.g. PyTorch) use shared memory to share data between processes, so if multiprocessing is used the default shared memory segment size that container runs with is not enough, and you should increase shared memory size either with `--ipc=host` or `--shm-size` command line options to `nvidia-docker run`.
```bash
nvidia-docker run -it --ipc=host ufoym/deepo bash
```


<a name="CPU"/>

## CPU Version

<a name="Installation-cpu"/>

### Installation

#### Step 1. Install [Docker](https://docs.docker.com/engine/installation/).

#### Step 2. Obtain the all-in-one image from [Docker Hub](https://hub.docker.com/r/ufoym/deepo)

```bash
docker pull ufoym/deepo:cpu
```

<a name="Usage-cpu"/>

### Usage

Now you can try this command:
```bash
docker run -it ufoym/deepo:cpu bash
```

If you want to share your data and configurations between the host (your machine or VM) and the container in which you are using Deepo, use the -v option, e.g.
```bash
docker run -it -v /host/data:/data -v /host/config:/config ufoym/deepo:cpu bash
```
This will make `/host/data` from the host visible as `/data` in the container, and `/host/config` as `/config`. Such isolation reduces the chances of your containerized experiments overwriting or using wrong data.

Please note that some frameworks (e.g. PyTorch) use shared memory to share data between processes, so if multiprocessing is used the default shared memory segment size that container runs with is not enough, and you should increase shared memory size either with `--ipc=host` or `--shm-size` command line options to `docker run`.
```bash
docker run -it --ipc=host ufoym/deepo:cpu bash
```


_You are now ready to begin your journey._


```$ python```
```python
>>> import tensorflow
>>> import sonnet
>>> import torch
>>> import keras
>>> import mxnet
>>> import cntk
>>> import chainer
>>> import theano
>>> import lasagne
>>> import caffe
>>> import caffe2
```

```$ caffe --version```
```
caffe version 1.0.0
```

```$ th```
```
 │  ______             __   |  Torch7
 │ /_  __/__  ________/ /   |  Scientific computing for Lua.
 │  / / / _ \/ __/ __/ _ \  |  Type ? for help
 │ /_/  \___/_/  \__/_//_/  |  https://github.com/torch
 │                          |  http://torch.ch
 │
 │th>
```


<a name="Customization"/>

# Customization

Note that `docker pull ufoym/deepo` mentioned in [Quick Start](#Quick-Start) will give you a standard image containing all available deep learning frameworks. You can customize your own environment as well.

<a name="One"/>

## Unhappy with all-in-one solution?

If you prefer a specific framework rather than an all-in-one image, just append a tag with the name of the framework.
Take tensorflow for example:
```bash
docker pull ufoym/deepo:tensorflow
```

<a name="Python"/>

## Other python versions

Note that all python-related images use `Python 3.6` by default. If you are unhappy with `Python 3.6`, you can also specify other python versions:
```bash
docker pull ufoym/deepo:py27
```

```bash
docker pull ufoym/deepo:tensorflow-py27
```

Currently, we support `Python 2.7` and `Python 3.6`.

See [Available Tags](#tags) for a complete list of all available tags. These pre-built images are all built from `docker/Dockerfile.*` and `circle.yml`. See [How to generate `docker/Dockerfile.*` and `circle.yml`](https://github.com/ufoym/deepo/tree/master/scripts) if you are interested in how these files are generated.

<a name="Jupyter"/>

## Jupyter support

#### Step 1. pull the image with jupyter support

```bash
docker pull ufoym/deepo:all-py36-jupyter
```

Note that the tag could be either of `all-py36-jupyter`, `py36-jupyter`, `all-py27-jupyter`, or `py27-jupyter`.

#### Step 2. run the image
```bash
nvidia-docker run -it -p 8888:8888 ufoym/deepo:all-py36-jupyter jupyter notebook --no-browser --ip=0.0.0.0 --allow-root --NotebookApp.token= --notebook-dir='/root'
```


<a name="Build"/>

## Build your own customized image with Lego-like modules

#### Step 1. prepare generator

```bash
git clone https://github.com/ufoym/deepo.git
cd deepo/generator
pip install -r requirements.txt
```

#### Step 2. generate your customized Dockerfile

For example, if you like `pytorch` and `lasagne`, then
```bash
python generate.py Dockerfile pytorch lasagne
```

This should generate a Dockerfile that contains everything for building `pytorch` and `lasagne`. Note that the generator can handle automatic dependency processing and topologically sort the lists. So you don't need to worry about missing dependencies and the list order.

You can also specify the version of Python:
```bash
python generate.py Dockerfile pytorch lasagne python==3.6
```

#### Step 3. build your Dockerfile

```bash
docker build -t my/deepo .
```

This may take several minutes as it compiles a few libraries from scratch.

---


<a name="Contributing"/>

# Contributing

We appreciate all contributions. If you are planning to contribute back bug-fixes, please do so without any further discussion. If you plan to contribute new features, utility functions or extensions, please first open an issue and discuss the feature with us.

