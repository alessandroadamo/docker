FROM ubuntu:latest

MAINTAINER Alessandro Adamo <alessandro.adamo@gmail.com>

ARG NB_USER=user
ARG NB_UID=1000

ARG WITH_NLTK=false
ARG WITH_OPENAI_GYM=false

ENV NB_USER $NB_USER
ENV NB_UID $NB_UID

ENV LC_ALL en_US.UTF-8
ENV LANG en_US.UTF-8
ENV LANGUAGE en_US.UTF-8

ENV WITH_NLTK $WITH_NLTK
ENV WITH_OPENAI_GYM $WITH_OPENAI_GYM

RUN apt-get -qq update && \
        apt-get -y dist-upgrade

RUN apt-get install -y \
	    curl \
	    gcc \
	    g++ \
	    gfortran \
	    build-essential \
            cmake \
	    locales \
	    fonts-dejavu

RUN echo "en_US.UTF-8 UTF-8" > /etc/locale.gen
RUN locale-gen

RUN apt-get install -y python3-pip && \ 
        pip3 install --upgrade pip 

RUN apt-get install -y \
        python3-dev \ 
        python3-opengl \
        xvfb \
        zlib1g-dev \
        libjpeg-dev \
        libav-tools \
        xorg-dev \
        libboost-all-dev \
        libsdl2-dev \
        swig  

RUN pip install \
        jupyter \
        ipykernel \
        jupyterlab

# Create user with UID=1000 and in the 'users' group
RUN adduser --home /home/$NB_USER \
        --shell /bin/bash \
        --uid 1000 user

USER user

# Setup user home directory
RUN mkdir /home/$NB_USER/notebook && \
    mkdir /home/$NB_USER/.jupyter && \
    mkdir /home/$NB_USER/.local

# Basic Packages
RUN pip install --upgrade --user --no-binary \
        numpy \
        scipy \
        pandas
        
RUN pip install --upgrade --user \
        iso8601 \
        urllib3 \
        h5py \
        # numpy \
        # scipy \
        # pandas \
        geojson \
        geopandas

# Tensorboard for Jupyter
RUN pip install --upgrade --user \
        jupyter-tensorboard

# Machine Learning packages
RUN pip install --upgrade --user --no-binary \
        scikit-learn

RUN pip install --upgrade --user \
        # scikit-learn \
        tensorflow \
        keras \
        keras-rl \
        tensorboard

# Visualization packages
RUN pip install --upgrade --user \
        matplotlib \
        seaborn \
        bokeh \
        plotly

# Natural Language Processing packages
RUN if [ $WITH_NLTK = true ]; then \
        pip install --upgrade --user \
            nltk \
            gensim; \ 
    fi

# Statistics packages
RUN pip install --upgrade --user \
        scrapy \
        statsmodels

# RL Enviroments (OpenAI Gym)
RUN if [ $WITH_OPENAI_GYM = true ]; then \
        pip install --upgrade --user gym && \
        pip install --upgrade --user "gym[atari]" && \
        pip install --upgrade --user "gym[box2d]" && \
        pip install --upgrade --user "gym[toy_text]" && \
        pip install --upgrade --user "gym[classic_control]"; \
    fi

# Configure container startup
WORKDIR /home/$NB_USER/notebook

# Configure the container volume
VOLUME /home/$NB_USER/notebook

ENTRYPOINT xvfb-run -s "-screen 0 1400x900x24" jupyter lab --port=8888 --no-browser --ip=0.0.0.0 --NotebookApp.token='' --NotebookApp.base_url=/ --NotebookApp.notebook_dir=/home/$NB_USER/notebook

# Jupyter port
EXPOSE 8888
