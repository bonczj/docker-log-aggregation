# Overview
The docker log aggregation project provides an easy to use installation process
 to configure and run log aggregation tools for docker containers. The goal is
to update the docker configuration so that all docker containers will pick up
a single log driver thereby avoiding the need to manually set it for each
container. The docker driver will then route all log messages to a log 
aggregation tool which can be used to view / search / sort through all of the
log messages at once. This configuration works well for applications that
are built using [12 Factor](https://12factor.net/) principles.

# Log Aggregation
## Docker Engine Logger
Docker daemon is configured by default to log every containers STDIN and
STDOUT to a JSON file on the docker host. This is accomplished by setting the 
docker daemon log driver to json-file.

This projects installation process updates the docker daemon to use the
fluentd log driver. This change will prevent the usage of the 'docker logs'
command. Instead, all log entries will be vieable through the log aggregation
UI.

## Fluentd daemon
The [fluentd](http://www.fluentd.org/) docker log driver requires that the fluentd daemon is available
through the docker host. This can be accomplished by running fluentd natively
on the docker host or by running a fluentd docker container. 

Running fluentd as a container does have a few draw backs. The log files for 
any other container will not be available unless the fluentd container is
already running.

The fluentd daemon will be run as a native process on the docker host to ensure
it is available for every docker container regardless how what the DevOps team
might have done to the environment. 

In particular, (FluentBit)[http://fluentbit.io/] will be used as the fluentd daemon 
running on the docker host. FluentBit is compatible with the fluentd daemon
but is lighter weight. The only forwarder that is required is to forward to 
Elasticsearch and that is built as a plugin to FluentBit. In order to have a 
distribution specific installation file for FluentBit, use the git repository
(fluent-bit-packaging)[https://github.com/fluent/fluent-bit-packaging]. The
FluentBit project does not include a pre-built binary for each Linux 
distribution, instead preferring to allow you to quickly build it yourself.

For reference, [http://fluentbit.io/articles/docker-logging-elasticsearch/].

## Elasticsearch
The [fluentd](http://www.fluentd.org/) process must write the log data somewhere
so that the information can be viewed or searched. Fluentd has a plugin to write
the data to Elasticsearch. This is one part of [Elastic Stack](https://www.elastic.co/webinars/introduction-elk-stack). 
By writing the log statements to Elasticsearch, the data was in indexed so that
it can be viewed and searched through Kibana (UI).

An off-the-shelf Elasticsearch container will be used to start as it provides 
enough capability out of the box without needing additional work.

## Kibana
Kibana provides a GUI that can view log data stored in Elasticsearch as part of
[Elastic Stack](https://www.elastic.co/webinars/introduction-elk-stack). 

An off-the-shelf Kibana container will be used to provide the GUI capability 
utilizing only minor configuration.

# Deployment
* Install fluentd package
* Install docker-log-aggregation package
  * Updates docker daemon log driver configuration
  * Installs/pulls required docker containers
  * Installs docker-log-aggregation systemd configuration
    * Starts required containers through docker-compose
* Validate logs by accessing through Kibana

# License
The docker log aggregation project is licensed under 
[GPLv3](https://github.com/bonczj/docker-log-aggregation/blob/master/LICENSE).