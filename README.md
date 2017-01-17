# Starting a Riak TS cluster
You can start a simple Riak TS cluster using docker-compose. Create a working directory named riak and inside that directory create a file named docker-compose.yml based on the following example.

Note: the coordinator node is the first one to be started in the cluster and is the node to which all the others will join. It's also the only container exposed on a predictable port.
Note: Alternatively you can pull the docker image from: docker pull basho/riak-ts

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
If you bring up the cluster now, you'll get a single-node cluster.

$ docker-compose up -d coordinator
When the cluster has started, open Riak Explorer in the browser by navigating to localhost:8098/admin/.

You can now create bucket types and Riak TS tables using the explorer web UI. There is also a comprehensive REST API your applications can leverage when interacting with this Dockerized cluster.

# Riak Cluster Explorer API
The Riak Explorer project provides REST APIs for data viewing and cluster management for Riak clusters.
https://basho-labs.github.io/riak_explorer/docs/api.html

