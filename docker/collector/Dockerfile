FROM python:3.10-slim-bullseye
ENV DEBIAN_FRONTEND noninteractive

ENV COLLECTOR '0.0.0.0'
ENV COLLECTOR_PORT '1883'
ENV MONGO_URL: 'mongodb://localhost:27017/'
ENV MONGO_COLLECTION: 'flows'

# update and install tshark
RUN apt-get update
RUN apt-get install -y gcc \
    libxml2-dev \
    libxslt-dev  \
    python3-lxml \
    python3-dev 

COPY requirements.txt requirements.txt
COPY modules/flows/ modules/flows/
COPY modules/mqtt_probe/ modules/mqtt_probe/
COPY modules/mqtt_collector/ modules/mqtt_collector/
COPY modules/netflow_tmp/ modules/netflow_tmp/
COPY collector.py collector.py
RUN python3 -m pip install -r requirements.txt
CMD ["sh", "-c", "python3 collector.py --host ${HOST} --port ${PORT} --mongo_url ${MONGO_URL} --mongo_collection ${MONGO_COLLECTION} --debug"]
