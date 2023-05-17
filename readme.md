# Probe & Collector
## MISSION - Process Aware Intrusion Detection in IoT Networks

> Intrusion Detection Systems (IDS) allow for detecting malicious activities in organizational networks and hosts. As the Industrial Internet of Things (Industrial IoT) has gained momentum and attackers become process-aware, it elevates the focus on anomaly-based Network Intrusion Detection Systems (NIDS) in IoT. While previous research has primarily concentrated on fortifying SCADA systems with NIDS, keeping track of the latest advancements in resource-efficient messaging (e.g., MQTT, CoAP, and OPC-UA) is paramount. In our work, we straightforwardly derive IoT processes for NIDS using distributed tracing and process mining. We introduce a pioneering framework called MISSION which effectively captures, consolidates, and models MQTT flows, leading to a heightened process awareness in NIDS. Through our prototypical implementation, we demonstrate exceptional performance and high-quality models. Moreover, our experiments provide empirical evidence for rediscovering pre-defined processes and successfully detecting two distinct MQTT attacks in a simulated IoT network. 

## Installation
```shell
pip install -r requirements.txt
```
## Probe
The probe captures network traffic from a given interface. It transforms the packets to IPFIX and sends it to a collector. In future it should support more protocols.  
### Command line
```shell
python probe.py --interface "LAN-Verbindung 2" --collector "localhost" --port 2055 --protocol mqtt --log-level "DEBUG"
```
### Docker 
The Dockerfile is located under [/docker/probe](https://git.uni-regensburg.de/misssion/probe/-/blob/master/docker/collector/Dockerfile).

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
The Dockerfile is located under [/docker/collector](https://git.uni-regensburg.de/misssion/probe/-/blob/master/docker/probe/Dockerfile).

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
