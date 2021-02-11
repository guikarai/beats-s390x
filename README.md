# Using Beats on s390x to monitor Linux on IBM Z security

Virtual servers for many is still the IT unit, and we know how many virtual servers can be hosted and managed by z/VM and KVM on IBM Z and LinuxONE.
Checking, and monitoring the compliance of such virtual servers over multiple criterias can be and hard task, and requires strong and easy to use tools.

The following is about explaining how I was able to Monitors the network and security logs on a pool of Linux on IBM Z and LinuxONE as easy as possible thanks to the Eleastic SIEM.

## What the ELK?

[Elastic Security](https://www.elastic.co/security) is a free and open application that offers many benefits to security teams: visibility, threat research, automated detection, and Security Operations Center (SOC) workflows. Elastic SIEM is part of the default distribution of the largest logging platform, the [Elastic Suite (ELK)](https://www.elastic.co/).

The [Beats](https://www.elastic.co/beats/) are lightweight data shippers, written in Go, that you install on your servers to capture all sorts of operational data (think of logs, metrics, or network packet data). 

The Beats send the operational data to [Elasticsearch](https://www.elastic.co/enterprise-search), either directly or via [Logstash](https://www.elastic.co/logstash), so it can be visualized with [Kibana](https://www.elastic.co/kibana).

By "lightweight", we mean that Beats have a small installation footprint, use limited system resources, and have no runtime dependencies.

## What ~~is~~ are Beats?

Beats are open source data shippers that you install as agents on your servers to send operational data to Elasticsearch. 
Elastic provides Beats for capturing:
|Description|The Beats experience|
|---|---|
|Collect your Linux audit framework data and monitor the integrity of your files.|Auditbeat|
|Tails and ships log files|Filebeat|
|Read and ships events from serverless infrastructure.|Functionbeat|
|Ping remote services for availability|Heartbeat|
|Read and ships event from Journald.|Journalbeat|
|Fetches sets of metrics from the operating system and services|Metricbeat|
|Monitors the network and applications by sniffing packets|Packetbeat|
|Fetches and ships Windows Event logs|Winlogbeat|

# Building or getting a Beats for s390x docker image

There are multi-posts, blogs and internet contribution on this topic, so I guess you will be able to build, and create the image you need.
From my side, I build my docker image, and put it on dockerhub so that it ease it access for my virtual server having an internet interface.

Please, contact me if interrested on that topic.

# Running beats in a docker container

There are many way to benefits the Beats experiences, but the simplest is from a docker container. It is fast to deploy and to configure that way.
Once you have your s390x beats image available on 

## Running a Beats container for Filebeat services

At first, you need to setup and review a configuration file for filebeat. This configuration file in my case is named filebeat.docker.yml.
Some items to be adapted and customized according to your configuration.

You cand below a sample of a filebeat.docker.yml:
```
filebeat.inputs:
- type: log
  enabled: false
  paths:
    - /var/host/log/*.log                         #<------- To be adapted

- type: filestream
  enabled: false
  paths:
    - /var/log/*.log

filebeat.config.modules:
  path: ${path.config}/modules.d/*.yml
  reload.enabled: false

setup.template.settings:
  index.number_of_shards: 1

setup.dashboards.enabled: true

setup.kibana:
  host: "XXX-XXX-XXX:5601"                        #<------- To be adapted
  username: "elastic"                             #<------- To be adapted
  password: "XXXXXXX"                             #<------- To be adapted

output.elasticsearch:
  hosts: ["XXX-XXX-XXX:9200"]                     #<------- To be adapted
  username: "elastic"                             #<------- To be adapted
  password: "XXXXXXX"                             #<------- To be adapted

processors:
  - add_host_metadata: ~
  - add_cloud_metadata: ~
  - add_docker_metadata: ~
  - add_kubernetes_metadata: ~
```

This configuration file will used and copied in the container to be created from the docker image.
Host logs to be copied in the container in a defined repository (to be synchronized with the filebeat.docker.yml).

To run a Filebeat docker container form a Beats, you can issue the following command:
```
docker run --cap-add=NET_ADMIN --network=host --name=elasticfilebeat --user=root \
    --volume="/root/beats/filebeat.docker.yml:/Beats/filebeat.yml:ro" \
    --volume="/var/log:/var/host/log:ro" \
    --volume="/root/beats/kibana:/Beats/kibana:ro" \
    --volume="/root/beats/modules.d:/Beats/modules.d:ro" \
    --volume="/root/beats/module:/Beats/module:ro" \
    --volume="/etc/timezone:/etc/timezone:ro" \
    --volume="/etc/localtime:/etc/localtime:ro" \
    --pid=host -e --strict.perms=false -e BEATSNAME=filebeat -d beats
```

It is fast to start the container, and if you are successful, you can confirm that it works thanks to the following command:
```
docker ps
CONTAINER ID        IMAGE                   COMMAND                  CREATED             STATUS              PORTS               NAMES
d29b4b4f5a2d        beats                   "/bin/sh -c '$BEATSN…"   3 days ago          Up 3 days                               elasticfilebeat
```

To be sure that everything is alright, you can read the logs of the running filebeat container thanks to the following command:
```
docker logs elasticfilebeat
[... truncated...]
2021-02-11T11:43:40.243+0100	INFO	[monitoring]	log/log.go:145	Non-zero metrics in the last 30s	{"monitoring": {"metrics": {"beat":{"cpu":{"system":{"ticks":29810,"time":{"ms":3}},"total":{"ticks":81200,"time":{"ms":11},"value":81200},"user":{"ticks":51390,"time":{"ms":8}}},"handles":{"limit":{"hard":1048576,"soft":1048576},"open":9},"info":{"ephemeral_id":"50c11bfc-9d4c-4529-ad63-b61dc779100e","uptime":{"ms":267333025}},"memstats":{"gc_next":8006144,"memory_alloc":4159768,"memory_total":4956839344},"runtime":{"goroutines":33}},"filebeat":{"harvester":{"open_files":0,"running":0}},"libbeat":{"config":{"module":{"running":1}},"pipeline":{"clients":2,"events":{"active":0}}},"registrar":{"states":{"current":4}},"system":{"load":{"1":0,"15":0,"5":0,"norm":{"1":0,"15":0,"5":0}}}}}}
[... truncated ...]
```
Connecting to the Elastic SIEM main dashboard on Kibana, you may see the following:


## Running a Beats container for Packetbeat services

# Overview of Elastic SIEM with Filebeats and Packetbeats
