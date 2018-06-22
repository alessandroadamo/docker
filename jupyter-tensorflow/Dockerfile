FROM ubuntu:18.04

MAINTAINER Alessandro Adamo <alessandro.adamo@gmail.com>

ARG NB_USER=user
ARG NB_UID=1000

ARG WITH_NLTK=false
ARG WITH_OPENAI_GYM=false
ARG WITH_OPENCV=false

ENV NB_USER $NB_USER
ENV NB_UID $NB_UID

ENV LC_ALL en_US.UTF-8
ENV LANG en_US.UTF-8
ENV LANGUAGE en_US.UTF-8

ENV TERM xterm
ENV TZ Europe/Rome

ENV WITH_NLTK $WITH_NLTK
ENV WITH_OPENAI_GYM $WITH_OPENAI_GYM
ENV WITH_OPENCV $WITH_OPENCV

RUN apt-get -qq update && \
        apt-get -y dist-upgrade

RUN DEBIAN_FRONTEND=noninteractive apt-get install -y \
            software-properties-common \
            wget \
            tzdata \
            dialog \
	    curl \
	    gcc \
	    g++ \
	    gfortran \
	    build-essential \
            cmake \
	    locales \
	    fonts-dejavu

RUN echo "en_US.UTF-8 UTF-8" > /etc/locale.gen && locale-gen
RUN cp /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

RUN apt-get install -y python3-pip && \ 
        pip3 install --upgrade pip 

RUN apt-get install -y \
        python3-dev \ 
        python3-opengl \
        xvfb \
        zlib1g-dev \
        libjpeg-dev \
#        libav-tools \
        ffmpeg \
        xorg-dev \
        libboost-all-dev \
        libsdl2-dev \
        swig

# install nodejs
RUN curl -sL https://deb.nodesource.com/setup_9.x
RUN apt-get install -y nodejs

RUN pip install \
        jupyter \
        ipykernel \
        jupyterlab

# OpenCV 
RUN if [ $WITH_OPENCV = true ]; then \
       apt-get install -y python3-opencv; \
    fi

# Install Java
RUN echo oracle-java8-installer shared/accepted-oracle-license-v1-1 select true | debconf-set-selections && \
    add-apt-repository -y ppa:webupd8team/java && \
    apt-get update && \
    apt-get install -y oracle-java8-installer && \
    rm -rf /var/lib/apt/lists/* && \
    rm -rf /var/cache/oracle-jdk8-installer
ENV JAVA_HOME /usr/lib/jvm/java-8-oracle

# Create user with UID=1000 and in the 'users' group
RUN adduser --home /home/$NB_USER \
        --shell /bin/bash \
        --uid 1000 user

USER $NB_USER

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
        requests \
        h5py \
        geojson \
        geopandas

# Tensorboard for Jupyter
RUN pip install --upgrade --user \
        jupyter-tensorboard

# Machine Learning packages
RUN pip install --upgrade --user  \
        scikit-learn

RUN pip install --upgrade --user \
        tensorflow \
        keras \
        tensorboard

# Visualization packages
RUN pip install --upgrade --user \
        matplotlib \
        ggplot \
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

# OpenCV
RUN if [ $WITH_OPENCV = true ]; then \
        pip install --upgrade --user \
            scikit-image \ 
            opencv-python \
            imgaug \
            imblearn \
            xlrd \
            pillow; \ 
    fi

WORKDIR /home/$NB_USER
ENV PATH /home/$NB_USER/.local/bin:$PATH

# Spark
RUN wget http://mirror.nohup.it/apache/spark/spark-2.3.1/spark-2.3.1-bin-hadoop2.7.tgz && \
        tar xvfz spark-2.3.1-bin-hadoop2.7.tgz && \
        ln -s spark-2.3.1-bin-hadoop2.7 spark
ENV SPARK_HOME=/home/$NB_USER/spark
ENV PYSPARK_SUBMIT_ARGS="--master local[4]" 
ENV PYSPARK_PYTHON=python3
ENV PYSPARK_DRIVER_PYTHON=jupyter 
ENV PYSPARK_DRIVER_PYTHON_OPTS="lab --port=8888 --no-browser --ip=0.0.0.0 --NotebookApp.token='' --NotebookApp.base_url=/ --NotebookApp.notebook_dir=/home/$NB_USER/notebook"
ENV PATH $SPARK_HOME/bin:$PATH

# MQTT, Kafka and Avro Libraries
RUN pip install --upgrade --user \
        avro-python3 \
        confluent-kafka \
        paho-mqtt
 
# Flask
RUN pip install --upgrade --user \
	flask

# Configure container startup
WORKDIR /home/$NB_USER/notebook

# Configure the container volume
VOLUME /home/$NB_USER/notebook

ENTRYPOINT xvfb-run -s "-screen 0 1400x900x24" pyspark
# ENTRYPOINT xvfb-run -s "-screen 0 1400x900x24" jupyter lab --port=8888 --no-browser --ip=0.0.0.0 --NotebookApp.token='' --NotebookApp.base_url=/ --NotebookApp.notebook_dir=/home/$NB_USER/notebook; \

# Jupyter port
EXPOSE 8888 5000 4040

