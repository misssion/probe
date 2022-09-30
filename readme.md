# MIND2 - Mining IoT Networks for Digital Twins

> Lorem ipsum dolor sit amet, consetetur sadipscing elitr, sed diam nonumy eirmod tempor invidunt ut labore et dolore magna aliquyam erat, sed diam voluptua. At vero eos et accusam et justo duo dolores et ea rebum. Stet clita kasd gubergren, no sea takimata sanctus est Lorem ipsum dolor sit amet. Lorem ipsum dolor sit amet, consetetur sadipscing elitr, sed diam nonumy eirmod tempor invidunt ut labore et dolore magna aliquyam erat, sed diam voluptua. At vero eos et accusam et justo duo dolores et ea rebum. Stet clita kasd gubergren, no sea takimata sanctus est Lorem ipsum dolor sit amet. Lorem ipsum dolor sit amet, consetetur sadipscing elitr, sed diam nonumy eirmod tempor invidunt ut labore et dolore magna aliquyam erat, sed diam voluptua. At vero eos et accusam et justo duo dolores et ea rebum. Stet clita kasd gubergren, no sea takimata sanctus est Lorem ipsum dolor sit amet.
## Installation
```shell
pip install -r requirements.txt
```
## Probe
The probe captures network traffic from a given interface. It transforms the packets to IPFIX and sends it to a collector. In future it should support more protocols. The benchmarking 
### Command line
```shell
python probe.py --interface "LAN-Verbindung 2" --collector "localhost" --port 2055 --protocol mqtt --log-level "DEBUG"
```
### Docker 
The Dockerfile is located under [/docker/probe](https://git.uni-regensburg.de/mind2/mind2/-/blob/master/docker/collector/Dockerfile).

### Docker compose
```yaml
services:
  probe:
    container_name: mqtt-probe
    image: mind2/probe
    environment:
      - INTERFACE=utun7
      - COLLECTOR=127.0.0.1
      - PORT=2055
      - PROTOCOL=mqtt
      - BENCHMARK=false
      - FILE_NAME=qos
      - LOG_LEVEL=DEBUG
    network_mode: "host"
    restart: "always"
    privileged: true
```
## Collector
The collectors aggregates IPFIX flows from different probes and stores them in a MongoDB.
### Command line
```shell
python collector.py --host "localhost" --port 2055 --mongo_url "mongodb://localhost:27017" --mongo_collection "flows"
```

### Docker 
The Dockerfile is located under [/docker/collector](https://git.uni-regensburg.de/mind2/mind2/-/blob/master/docker/probe/Dockerfile).

### Docker compose
```yaml
services:
  collector:
    container_name: collector
    image: mind2/collector:latest
    depends_on:
      - mongodb
    environment:
      - COLLECTOR=collector
      - COLLECTOR_PORT=2055
      - MONGO_URL=mongodb://mongodb:27017/
      - MONGO_COLLECTION=flows
    ports:
      - "2055:2055"
    restart: always
  mongodb:
    container_name: mongodb
    image: mongo:latest
    ports:
      - "27017:27017"
    restart: always
```
## Analysis
The analysis module is able to preprocess the flows inside the MongoDB and mines processes on these flows. This module allows the generation of directly follows graphs and the application of the Alpha miner, Heuristic miner, and Inductive miner. Further it encompasses an evaluation flag to quantify the quality of the model regarding it's replay fitness, precision, simplicity, and generalization. 
```shell
python --mongodb "mongodb://localhost:27017" --schema "evaluation" --limit 100000 --miner "alpha" --preprocessing "True" --graphs "True" --evaluation "True" 
```


