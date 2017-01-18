# Overview
I was looking at Time series databases to store my analytics data, and I found two options, Influx DB and Riak. I think Influx is in the market for a long time and is one of the guys who support both Regular and Iregular time series (not evenly distributed). 

I found Riak, which sounds really cool. So I decided to document my experience, maybe someone needs to get a quick and short review on it.

There are plenty of doucments and materials on http://basho.com

# What is Riak (specially Riak TS)? 
The Internet of Things (IoT) or the Internet of Everything is changing the way companies interact with their customers and manage their data. 

These connected devices generate high volume time series data that can be created in milliseconds. This fast growth of IoT data and other time series data is producing challenges for enterprise applications where data must
be collected, saved, and analyzed in the blink of an eye. 

Your application needs a database built to uniquely handle time series data to ensure your data is continuously available and accurate.

# Database Models

Riak KV: Usecases

- Mutable Data
- Documents, JSON, metadata
- Session state
- User/customer data
- Transactions histories
- Archives


Riak TS: Usercase

- Immutable Data
- Infra. Monitoring / metrics
- Real-time analytics
- IoT / Sensor Data
- Financial Data
- Scientific Observations

# Starting a Riak TS cluster
You can start a simple Riak TS cluster using docker-compose. Create a working directory named riak and inside that directory create a file named docker-compose.yml based on the following example.

Note: the coordinator node is the first one to be started in the cluster and is the node to which all the others will join. It's also the only container exposed on a predictable port.
Note: Alternatively you can pull the docker image from: 
```
$ docker pull basho/riak-ts
```
Docker compose file is: 

```
version: "2"
services:
  coordinator:
    image: basho/riak-ts
    ports:
      - "8087:8087"
      - "8098:8098"
    environment:
      - CLUSTER_NAME=riakts
    labels:
      - "com.basho.riak.cluster.name=riakts"
    volumes:
      - schemas:/etc/riak/schemas
  member:
    image: basho/riak-ts
    ports:
      - "8087"
      - "8098"
    labels:
      - "com.basho.riak.cluster.name=riakts"
    links:
      - coordinator
    depends_on:
      - coordinator
    environment:
      - CLUSTER_NAME=riakts
      - COORDINATOR_NODE=coordinator

volumes:
  schemas:
    external: false
```

If you bring up the cluster now, you'll get a single-node cluster.
```javascript
$ docker-compose up -d coordinator
```
When the cluster has started, open Riak Explorer in the browser by navigating to localhost:8098/admin/.

You can now create bucket types and Riak TS tables using the explorer web UI. There is also a comprehensive REST API your applications can leverage when interacting with this Dockerized cluster.

# Riak Cluster Explorer API
The Riak Explorer project provides REST APIs for data viewing and cluster management for Riak clusters.
https://basho-labs.github.io/riak_explorer/docs/api.html

# Riak TS Admin Dashboard
You can access Riak TS admin dashboard on: http://localhost:8098/admin

You can also communicate with Riak using admin command: 

```
# riak-admin
Usage: riak-admin { cluster | join | leave | backup | restore | test |
                    reip | js-reload | erl-reload | wait-for-service |
                    ringready | transfers | force-remove | down |
                    cluster-info | member-status | ring-status | vnode-status |
                    aae-status | diag | stat | status | transfer-limit | reformat-indexes |
                    top [-interval N] [-sort reductions|memory|msg_q] [-lines N] |
                    downgrade-objects | security | bucket-type | repair-2i |
                    search | services | ensemble-status | handoff | set |
                    timezone | show | describe }

```
# Riak TS: Write Performance
- 130K writes / second
- 5 nodes (bare metal, softlayer)
- 6-cores + HT (12 logical cores)
- 32 GB
- 800 GB SSD x3 (RAID0)

# References

- Basho (basho.com)
- Slideshare (http://www.slideshare.net/clibou/riak-ts)
- Riak TS Getting Started (http://info.basho.com/rs/721-DGT-611/images/Getting-Started-with-Riak-TS.pdf)
- Riak TS Ref Arc (http://info.basho.com/rs/721-DGT-611/images/RiakTS-Enterprise-Technical-Overview.PDF)

