---
id: docker-active-rep
title: Getting Started with Active Replication
sidebar_label: Active-Replication Example
---

## Set up a network, run active replica servers, connect clients and test 

### set up a network for our docker containers. 
```
$ docker network create mynetwork		
```
*you can view the network with ` $ docker network ls `*
### launch nodes
```
$ docker run --name mynode1 --net mynetwork eqalpha/keydb
```
### To launch with custom config file:
```
$ docker run -v /path-to-config-file/redis.conf:/etc/keydb/redis.conf --net mynetwork --name mynode1 -d eqalpha/keydb
```
### To launch with updates to current config file:
```
$ docker run --net mynetwork --name mynode1 -d eqalpha/keydb keydb-server /etc/keydb/redis.conf --active-replica yes
```
### The following may assist when updating redis.config obtained from our [github page](https://github.com/EQ-Alpha/KeyDB) or use the updated one contained in this example:
Comment out “bind 127.0.0.1”, change "protected-mode" from yes to no. If you want to enable Active Replica Support then uncomment "active-replica yes".
### To get the ipaddress of the node you just made: 
```
$ docker inspect -f '{{ (index .NetworkSettings.Networks "mynetwork").IPAddress }}' mynode1
```
If you wanted to run a client to connect to a node on this network:
```
$ docker run -it --net mynetwork eqalpha/keydb keydb-cli -h <ip-address-of-node> -p 6379
```

### Test:
Try creating two nodes, then connect one client to each as stated above. Once you are connected to the keydb server with keydb-cli, call each node to be a replica of the other node via ` REPLICAOF <your-node-ip-address>:6379 `

Once both nodes are replicas of eachother you can write ` SET testval1 hello ` into one client, then on the next client write ` GET testval1 ` you should see the response "hello".
