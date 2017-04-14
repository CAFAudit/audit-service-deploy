## Audit Service Deployment

### Introduction

The Audit Service is designed to provide auditing of user and system actions by defining the required events and the information associated with each event.  This definition is registered with the Audit service and used to create a Java SDK to record the audited events.  The Audit service is multi-tenant aware, which requires an application producing events to register each new tenant.  Applications send events using the generated Java SDK to Elasticsearch where they are Indexed according to tenant.

### Deployment Repository

This repository provides the necessary files to easily get started using the Audit Service.

The only pre-requisite required to get started is that Docker must be available on the system.

The deployment files are in Docker Compose v3 format, and they are compatible with both Docker Compose and Docker Stack.

The deployment file references at present only references Elasticsearch.

### Demonstration

The Docker Compose file contains the following services:


**Elasticsearch**
[Elasticsearch](https://www.elastic.co/products/elasticsearch) is a search engine based on [Lucene](https://lucene.apache.org/core/). It provides a distributed, multitenant-capable full-text search engine with an HTTP web interface and schema-free JSON documents. Elasticsearch is developed in Java and is released as open source under the terms of the Apache License.

###Usage

1. Download the files from this repository
You can clone this repository using Git or else you can simply download the files as a Zip using the following link:
https://github.com/CAFAudit/audit-service-deploy/archive/develop.zip

2. Configure the external parameters if required
The following parameters may be set:

    <table>
      <tr>
        <th>Environment Variable</th>
        <th>Default</th>
        <th>Description</th>
      </tr>
      <tr>
        <td>ES_JAVA_OPTS</td>
        <td>-Xmx256m -Xms256m</td>
        <td>Environment variable to set heap size, e.g. to use 1GB use ES_JAVA_OPTS="-Xms1g -Xmx1g"</td>
      </tr>
      <tr>
        <td>xpack.security.enabled</td>
        <td>false</td>
        <td>Set to false to disable X-Pack security</td>
      </tr>
      <tr>
        <td>xpack.monitoring.enabled</td>
        <td>false</td>
        <td>Set to false to disable X-Pack monitoring</td>
      </tr>
      <tr>
        <td>xpack.graph.enabled</td>
        <td>false</td>
        <td>Set to false to disable X-Pack graph</td>
      </tr>
      <tr>
        <td>xpack.watcher.enabled</td>
        <td>false</td>
        <td>Set to false to disable Watcher</td>
      </tr>
    </table>

For more information on X-Pack:
* https://www.elastic.co/guide/en/x-pack/current/xpack-settings.html
* https://www.elastic.co/guide/en/x-pack/current/installing-xpack.html#xpack-enabling

3. Deploy the services
First navigate to the folder where you have downloaded the files to and then run one of the following commands, depending on whether you are using Docker Compose or Docker Stack:

    <table>
      <tr>
        <td><b>Docker Compose</b></td>
        <td>docker-compose up</td>
      </tr>
      <tr>
        <td><b>Docker Stack</b></td>
        <td>docker stack deploy --compose-file=docker-compose.yml auditservicedemo</td>
      </tr>
    </table>

4. Check the Health of Elasticsearch
The health of the Elasticsearch container and / or cluster can be inspect by issuing the following command:  
* `curl elastic http://127.0.0.1:9200/_cat/health`  
* `1472225929 15:38:49 docker-cluster green 2 2 4 2 0 0 0 0 - 100.0%`

### Troubleshooting

##### Errors during Start Up

If the following error appears during start up and the Elasticsearch container shuts down shortly after start up:
> elasticsearch_1  | ERROR: bootstrap checks failed  
> elasticsearch_1  | max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]

This error can be resolved by issuing the following command on the Linux host:  
* `sudo sysctl -w vm.max_map_count=262144`

More information regarding `vm.max_map_count` can be found in the Elasticsearch official Documentation [here](https://www.elastic.co/guide/en/elasticsearch/reference/current/docker.html#docker-cli-run-prod-mode) and [here](https://www.elastic.co/guide/en/elasticsearch/reference/current/vm-max-map-count.html).

##### Warnings during Start Up

If the following warning is seen during start up it can be disregarded:
> audit_elasticsearch | [2017-04-14T09:20:02,597][WARN ][i.n.u.i.MacAddressUtil   ] Failed to find a usable hardware address from the network interfaces; using random bytes: 1a:e1:8d:83:20:f5:d0:3c

More information can be found [here](https://discuss.elastic.co/t/es-5-2-0-in-kvm-netty-warning-failed-to-find-a-usable-hardware-address-from-the-network-interfaces/73717/3)
 