# jupyter-tensorflow

This repository contains Dockerfile for [Jupyter](https://www.jupyter.org)
container, based on the [Ubuntu](https://hub.docker.com/r/_/ubuntu/) image.

## Install

As a prerequisite, you need [Docker](https://docker.com) to be installed.

To build this image from the dockerfile:

	$ docker build -t jupyter-tensorflow .

To change the default user and user id:

	$ docker build -t jupyter-tensorflow . --build-arg NB_USER=user --build-arg NB_UID=1000

To enable / disable the [Natural Language Toolkit](https://www.nltk.org):

	$ docker build -t jupyter-tensorflow . --build-arg WITH_NLTK=true

To enable / disable the [OpenAI Gym](https://gym.openai.com):

	$ docker build -t jupyter-tensorflow . --build-arg WITH_OPENAI_GYM=true

## Usage

To run `jupyter-tensorflow`:

	$ docker run -it --rm --name jupyter-tensorflow-nb -p 8888:8888 -v /home/user/notebook:/home/user/notebook jupyter-tensorflow

To access to `JupyterLab` from the browser:

	http:\\localhost:8888

To access to the old `Jupyter` interface from the browser:
	
	http:\\localhost:8888\tree?

