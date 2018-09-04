FROM ubuntu:18.04

RUN apt-get update  && apt-get -y dist-upgrade 
RUN apt-get  -y install software-properties-common python3-software-properties debconf-utils

# install Oracle Java 8

RUN add-apt-repository ppa:webupd8team/java  && apt-get update
RUN echo "oracle-java8-installer shared/accepted-oracle-license-v1-1 select true" | debconf-set-selections
RUN apt-get install -y oracle-java8-installer

# install ActiveMQ

WORKDIR /opt
RUN wget http://archive.apache.org/dist/activemq/5.15.5/apache-activemq-5.15.5-bin.tar.gz
RUN tar -xvf ./apache-activemq-5.15.5-bin.tar.gz
RUN ln -s apache-activemq-5.15.5 apache-activemq

COPY activemq.xml /opt/apache-activemq/conf/activemq.xml

CMD /opt/apache-activemq/bin/activemq start

EXPOSE 61616    # OpenWire
EXPOSE 5672      # AMQP 
EXPOSE 61613    # STOMP
EXPOSE 1883      # MQTT
EXPOSE 	61614    # WS
