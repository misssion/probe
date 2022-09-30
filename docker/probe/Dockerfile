FROM python:3.10-slim-bullseye

ENV DEBIAN_FRONTEND noninteractive

# environment variables
ENV INTERFACE 'any'
ENV COLLECTOR '127.0.0.1'
ENV PORT '1883'
ENV PROTOCOL 'mqtt'
ENV BENCHMARK 'false'
ENV FILE_NAME 'qos'
ENV LOG_LEVEL 'DEBUG'

# update and install tshark
RUN apt-get update
RUN apt-get install -y tshark \
    # for ARM architectures
    gcc \ 
    # for ARM/v7 architectures
    libxml2-dev \
    libxslt-dev  \
    python3-lxml \
    python3-dev 

# copy essential dirs and files
COPY requirements.txt requirements.txt
COPY modules/flows/ modules/flows/
COPY modules/mqtt_probe/ modules/mqtt_probe/
COPY modules/mqtt_collector/ modules/mqtt_collector/
COPY modules/netflow_tmp/ modules/netflow_tmp/
COPY probe.py probe.py

# install python dependencies
RUN python3 -m pip install -r requirements.txt

# Run command
CMD ["sh", "-c", "python3 probe.py --collector ${COLLECTOR} --port ${PORT} --interface ${INTERFACE} --protocol ${PROTOCOL} --benchmark ${BENCHMARK} --file-name ${FILE_NAME} --log-level ${LOG_LEVEL} "]
