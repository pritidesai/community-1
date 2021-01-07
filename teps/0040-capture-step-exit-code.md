---
status: proposed
title: 'Capture Step Exit Code'
creation-date: '2021-01-06'
last-updated: '2021-01-06'
authors:
- '@pritidesai'
- '@afrittoli'
---

# TEP-0040: Capture Step Exit Code

<!-- toc -->
- [Summary](#summary)
- [Motivation](#motivation)
  - [Goals](#goals)
  - [Non-Goals](#non-goals)
  - [Use Cases](#use-cases)
- [References](#references)
<!-- /toc -->

## Summary

Tekton tasks are defined as a collection of steps in which each step can specify a container image to run.
Steps are executed in order in which they are specified. One single step failure results in a task failure
in turn results in a pipeline failure. Once a step results in failure, rest of the steps are not executed.

Many common pipelines have requirement where a step failure must not halt the entire execution. In order to
continue executing a task even if a step fail, pipeline authors have flexibility of wrapping an image and
exiting that task with success. This changes the failing task into success and does not block further
execution. This is a workaround and only works with images which can be wrapped. This workaround does not apply
to off-the-shelf container images.

As a pipeline execution engine, we want to support off-the-shelf container image as a step and provide
an option to capture non-zero exit code. The pipeline author can choose to continue execution after capturing
the non-zero exit code and make it available to access it after the execution.

Issue: [tektoncd/pipeline#2800](https://github.com/tektoncd/pipeline/issues/2800)


## Motivation

It should be possible to easily use off-the-shelves (OTS) images as steps in Tekton tasks. A task author has no
control on the image but may desire to capture an error and continue executing the pipeline.

### Goals

Design a step failure strategy so that the task author can control the behaviour of the image and decide to
continue execution or not.

Store the container termination state or error state and make it accessible after the pipeline finishes execution.

This proposal must be applicable to any container image including custom images and off-the-shelf images.

### Non-Goals

This design is limited to a step within a task and does not apply to pipeline tasks.

### Use Cases

As a pipeline author, I would like to design a task with multiple steps. One of the steps is running an
enterprise  image to run unit tests, and the next step needs to report test results even after a previous
step results in failure due to tests failure.


## References

* https://github.com/tektoncd/pipeline/issues/2800