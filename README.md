# Using Beats on s390x to monitor Linux on IBM Z security

## What is Beats?

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

## Why Beats on s390x?



# Running beats in a docker container

## Running a Beats container for Filebeat services

## Running a Beats container for Packetbeat services

# Overview of Elastic SIEM with Filebeats and Packetbeats
