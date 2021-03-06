# pika-traffic-generator
A simple application for generating traffic to a server based on its nginx logs indexed on a elasticsearch database.
 

## Installing / Getting started

To quick setting up a batch instance run 

```shell
docker-compose -f docker-compose.production.yml up
```


## Developing

### Built With

The project is build using spring boot 1.3.8 framework. This version is used due elasticsearch server on pikamachu runs 1.1.1 version an the latest spring data elasticsearch version that supports it is the 1.3.x.

### Prerequisites

To set up the environment the next sofware must be installed on development machine
* Install jdk 8
* Install git
* Configure pikamachu Maven settings
* Recommended IDE eclipse Oxygen
* Access to pikamachu intranet

### Setting up Dev

To set up project for eclipse:

```shell
git clone https://github.com/pikamachu/pika-traffic-generator.git
cd pika-traffic-generator/
gradlew eclipse
```

Configure proxy user and password if needed on gradle.properties

```shell
# System Proxy settings
systemProp.http.proxyHost=
systemProp.http.proxyPort=
systemProp.http.proxyUser=
systemProp.http.proxyPassword=
systemProp.https.proxyHost=
systemProp.https.proxyPort=
systemProp.https.proxyUser=
systemProp.https.proxyPassword=
```

### Building

To build execute gradlew build task:

```shell
gradlew build
```

To run the application execute gradlew bootRun task:

```shell
gradlew bootRun
```

The application can also be run as a docker container 

```shell
docker-compose up
```

## Configuration

The application configuration can be modified using application.properties file. The applications also uses spring boot profiles.

```shell
#########################################################
#              Elasticsearch client config              #
#########################################################
# Elasticsearch client settings
traffic.elasticsearch.host=10.3.0.120
traffic.elasticsearch.port=9300

#########################################################
#               Traffic batch config                    #
#########################################################
# Batch setting Elasticsearch Logs
traffic.batch.config.jobName=elasticsearch-logs-bot-job
traffic.batch.config.stepPrefixName=elasticsearch-logs-step
traffic.batch.config.flowPrefixName=elasticsearch-logs-flow
# Defines the request burst
traffic.batch.config.stepsChunk=100
# Defines the number of concurrent users
traffic.batch.config.concurrentFlows=1
# Defines the maximum time in second the batch will run 
traffic.batch.config.timeLimit=0
# Defines the ramp-up period in seconds for full processing of defined concurrent flows 
traffic.batch.config.rampUp=100
# Enable batch thread stepped distribution of timestamp range
# timestampRangeFlow(n) = timestampRange - ((n * timestampRange)/(concurrentFlows + 1))
traffic.batch.config.steppedDistributionFlow=false
# List of locales that will be used to generate traffic
traffic.batch.config.locales=es_ES,fr_FR,it_IT,fr_BE,de_AT,pt_PT,nl_NL,en_GB,en_US,en_CA

#########################################################
#               Traffic batch reader                    #
#########################################################
# Real traffic search range for timestamp in millis. 
#   "to" is currentTime.
#   initial "from" is currentTime - timestampRange, then is updated with logs timestamp
# 1 day -> 86400000
# 12 hours -> 43200000
# 1 hour -> 1800000
# 5 min -> 300000
traffic.batch.reader.timestampRange=1800000
# Max results on searches
traffic.batch.reader.maxPageSize=1000
# Wait time in no results found
traffic.batch.reader.noMoreLogsFoundSleepTime=30000
# Finish process if no more logs found
traffic.batch.reader.finishOnNoMoreLogsFound=false
# Include only request that match this query
traffic.batch.reader.includeOnlyRequestsQuery=*
# Not include request that match this query
#traffic.batch.reader.discardRequestsQuery=*components*

#########################################################
#            Traffic http service config                #
#########################################################
# Client domain
traffic.request.service.domain=kkdelavk.com
traffic.request.service.protocol=http://
traffic.request.service.authorization=Basic RDNzMTRrcTRCWA==
# Store cookies and reuse on next requests
traffic.request.service.useCookies=true
# Reset cookies after this number of requests
traffic.request.service.resetCookiesRequests=100

#########################################################
#            Traffic stats service config                #
#########################################################
# Enable stats
traffic.stats.service.enabled=true
traffic.stats.service.withDetails=false

#########################################################
#               Log Levels config                       #
#########################################################
logging.level.=INFO
logging.level.org.springframework.batch=INFO
logging.level.com.pikamachu.trafficgen=INFO
logging.level.org.springframework.data.elasticsearch=DEBUG

#########################################################
#               Sprint thymeleaf config                 #
#########################################################
spring.thymeleaf.check-template-location=true
spring.thymeleaf.prefix=classpath:templates/
spring.thymeleaf.suffix=.html
spring.thymeleaf.mode=HTML5
spring.thymeleaf.encoding=UTF-8
spring.thymeleaf.content-type=text/html
spring.thymeleaf.cache=false

#########################################################
#               Sprint boot tunnings                    #
#########################################################
# Application Name
spring.application.name=pika-traffic-generator
# Profile selection
spring.profile=default
# Disable Sprint boot web features
#spring.main.web-environment=false
# Disable auto h2 db
spring.autoconfigure.exclude=org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration
# Spring server port
server.port=8090
# Spring boot admin server
spring.boot.admin.url=http://10.3.0.42:9090


```
