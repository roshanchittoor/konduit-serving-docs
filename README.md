---
description: >-
  Konduit Serving is a serving system and framework focused on deploying machine
  learning pipelines to production.
---

# Introduction

## Overview

Konduit Serving provides building blocks for developers to write their own production machine learning pipelines from pre-processing to model serving, exposable as a simple REST API.

The core abstraction is an idea called a **pipeline step**. A pipeline step performs a task such as: 

1. pre-processing steps;
2. running one or more machine learning models; and
3. post-processing steps: transforming the output in a way that can be understood by humans, such as labels in a classification example,

as part of using a machine learning model in a deployment scenario. 

For instance, a `ModelStep` performs inference on a \(mix of\) TensorFlow, Keras, Deeplearning4j \(DL4J\) or Predictive Model Markup Language \(PMML\) models.

{% page-ref page="examples/python/tensorflow-model-serving/" %}

{% page-ref page="examples/python/dl4j.md" %}

{% page-ref page="examples/python/keras.md" %}

A custom pipeline step can be built using a `PythonStep`. This allows you to embed pre- or post-processing steps into your machine learning pipeline, or to serve models built in frameworks that do not have built-in`ModelStep`s such as scikit-learn and PyTorch.

{% page-ref page="examples/python/onnx.md" %}

Konduit Serving also contains functionality for other pre-processing tasks, such as DataVec transform processes and image transforms.

{% page-ref page="examples/python/datavec.md" %}



## Usage

One way to configure a Konduit Serving instance is by using a [YAML file](yaml-configurations.md). The following YAML file configures a Konduit Serving instance to run a short Python script as specified in the `python_code` argument:

```yaml
serving:
  http_port: 1337
  input_data_format: NUMPY
  output_data_format: NUMPY
steps:
  python_step:
    type: PYTHON
    python_code: |
      first += 2
      second = first
    python_inputs:
      first: NDARRAY
    python_outputs:
      second: NDARRAY
client:
    port: 1337
```

[Installing the Konduit Serving Python SDK](installation.md) exposes the `konduit` command line interface \(CLI\). Assuming the YAML file above is saved in the current directory as `hello-world.yaml`, start a Konduit Serving instance by running the following code in the command line:

```bash
konduit serve --config hello-world.yaml
```

This exposes a REST API for sending data to the server for inference. Inputs can be sent using the CLI, the Python SDK or any other application that supports sending HTTP POST requests such as [requests ](https://requests.readthedocs.io/en/master/)or [UiPath](https://docs.uipath.com/activities/docs/http-client) (for RPA-based workflows\). 

Finally, stop the Konduit Serving instance: 

```bash
konduit stop-server --config hello-world.yaml
```

To get started with Konduit Serving, check out the Quickstart page.

{% page-ref page="quickstart/" %}

## Why Konduit Serving?

### Python-first

We strive to provide a Python-first SDK that makes it easy to integrate Konduit Serving into a Python-first workflow.

{% page-ref page="model-monitoring/monitoring-grafana.md" %}

### Modern visualization standards

We want to expose [modern standards](http://prometheus.io/) for monitoring everything from your GPU to your inference time. Konduit Serving supports visualization applications such as [Grafana](http://grafana.com) that support the [Prometheus](https://prometheus.io/) standard for visualizing data.

### Performance and security

Konduit Serving was built with the goal of providing proper low-level interoperability with native math libraries such as TensorFlow and DL4J's core math library libnd4j. At the core of Konduit Serving are the [JavaCPP Presets](https://github.com/bytedeco/javacpp-presets), [Vert.x](http://vertx.io) and DL4J for running Keras models in Java.

Combining JavaCPP's low-level access to C-like APIs from Java with Java's robust server-side application development \(Vert.x on top of [netty](http://netty.io/)\) allows for better access to faster math code in production while minimizing the surface area where native code = more security flaws \(mainly in server side networked applications\). This allows us to do things like zero-copy memory access of NumPy arrays or Arrow records for consumption straight from the server without copy or serialization overhead. Extending that to Python SDK, we know when to return a raw Arrow record and return it as a pandas DataFrame.

When dealing with deep learning, we can handle proper inference on the GPU \(batching large workloads\).

### Java microservices

A Vert.x-based model server and pipeline development framework allows a thin abstraction that can be embedded in a Java microservice.

### Enterprise integration

We aim to provide integrations with more enterprise platforms typically seen outside the big data space.

