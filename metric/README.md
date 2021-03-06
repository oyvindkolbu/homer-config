
## About
With following kamailio configuration files you are able to send metrics to Elasticsearch, Graylog and InfluxDB. 
You may asking yourself why we choose this approach despite the fact that homer already has some kind of metrics 
(methods, responses, kpi's) plus rudimentary alerting and the possibility to graph them. First of all the former 
solution stores all the metrics inside Mysql or Postgres. Both are great Databases but there are better solutions 
to store timeseries data in an most efficient way. The same applies to the former alerting solution. While it can 
be used for very basic scenarios like sending an E-Mail when different kinds of thresholds exceeded it's simply 
not flexible and powerfull enough to rely on it. Last but not least the homer team spend a lot of time to provide 
some kind of dashboards to graph the metrics from the internal database or graph some metrics from external sources.
Time which could be spend to provide you the ability to capture a larger range of protocols and show you the 
correlation between them. Or simply spoken to improve the core aspects of homer without trying to reinvent the wheel.

### Structure
The main entry point is the kamailio.cfg. From here you can configure:

* Parameters for Homer
* Parameters for Elasticsearch, Graylog, InfluxDB
* Choose the functions you need. In the form of uncommenting them
* Easily integrate your own functions

For example if you want to send every 1 second kpi, geo and xrtp metrics to InfluxDB.

```bash

/* Parameters for InfluxDB */
#!substdef "!INFLUXDB_HTTP_URL!http://192.168.2.1:8086!g"
#!substdef "!INFLUXDB_DB!homer!g"
#!substdef "!INFLUXDB_PRECISION!u!g"
#!substdef "!INFLUXDB_RETENTION!autogen!g"


/* Parameters for the rtimer module. How often (seconds) stats will be send. */
#!substdef "!CHECK_STATS_INTERVAL!1!g"


##!define   DO_ELASTICSEARCH
##!define   DO_GRAYLOG
#!define    DO_INFLUXDB
#!define    DO_GEO
##!define   DO_ISUP
#!define    DO_KPI
##!define   DO_MALICIOUS
##!define   DO_METHOD
##!define   DO_RESPONSE
##!define   DO_RTCPXR
##!define   DO_USERAGENT
##!define   DO_XHTTP
#!define    DO_XRTP

...

```


Out of the box only SIP, RTCP (rotated) and Logs if present will be stored.

### Getting Started

To install homer with these configuration files you can use the homer-installer from the 
kamailio-5.x-metric branch.

```bash
git clone https://github.com/sipcapture/homer-installer.git -b kamailio-5.x-metric
cd homer-installer
./homer_installer.sh
```

The fastest way to get a Elasticsearch, Graylog or InfluxDB playground is docker. Let's 
assume your PublicIP is 165.227.138.11.


#### Elasticsearch

To get started with Elasticsearch simply run

* docker run -d -p 9200:9200 -p 5601:5601 nshou/elasticsearch-kibana

Change inside the kamailio.cfg the Elasticsearch parts and restart kamailio.

```bash
/* Parameters for Elasticsearch */
#!substdef "!ELASTICSEARCH_HTTP_URL!http://127.0.0.1:9200!g"
To
#!substdef "!ELASTICSEARCH_HTTP_URL!http://165.227.138.11:9200!g"

##!define   DO_ELASTICSEARCH
To
#!define   DO_ELASTICSEARCH
```

Visit http://165.227.138.11:5601 and set homer-* as Index. After one minute you should see
something like this:

![ImgurElasticsearch](http://i.imgur.com/GSsmZUA.png)


#### Graylog

To get started with Graylog you can use the provided docker/Graylog/docker-compose.yml 
file. Before you can run it you have to change inside the docker-compose.yml the 
GRAYLOG_WEB_ENDPOINT_URI to your PublicIP 165.227.138.11:

```bash
- GRAYLOG_WEB_ENDPOINT_URI=http://127.0.0.1:9000/api
To
- GRAYLOG_WEB_ENDPOINT_URI=http://165.227.138.11:9000/api
```

Now you can run from the Graylog folder:

```bash
docker-compose up -d
```

After some moments you can navigate to http://165.227.138.11:9000. Login with admin/admin 
and launch a new global GELF HTTP input under System/Inputs. Choose a name and simply use the default values like here:

![ImgurGraylog](http://i.imgur.com/AX0GTN2.png)

Change inside the kamailio.cfg the Graylog parts and restart kamailio.

```bash
/* Parameters for Graylog */
#!substdef "!GRAYLOG_GELF_HTTP_URL!http://127.0.0.1:12201!g"
To
#!substdef "!GRAYLOG_GELF_HTTP_URL!http://165.227.138.11:12201!g"

##!define   DO_GRAYLOG
To
#!define   DO_GRAYLOG
```


#### InfluxDB

To get started with InfluxDB you can use the provided docker/TICK/docker-compose.yml 
file. Just run from the TICK folder:

```bash
docker-compose up -d
```

Change inside the kamailio.cfg the InfluxDB parts and restart kamailio.

```bash
/* Parameters for Elasticsearch */
#!substdef "!INFLUXDB_HTTP_URL!http://127.0.0.1:8086!g"
To
#!substdef "!INFLUXDB_HTTP_URL!http://165.227.138.11:8086!g"

##!define 	DO_INFLUXDB
To
#!define 	DO_INFLUXDB
```

Visit http://165.227.138.11:8888 go to the Data Explorer tab and build your first query.

![Imgur](http://i.imgur.com/vbegi8G.png)