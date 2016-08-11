# elasticsearch.yml


**Elasticsearch Configuration**

- By default `elasticsearch.yml` comes with out of the box support with default settings. It doesn't need to be changed too much
- By default the configuration file contains all the required template items. Using there is the best way to start to build a production level Elasticsearch


**Cluster Name**

- Do not forget to give your production cluster a name as this is used to discover and auto-join other nodes
- Don’t reuse the same cluster names in different environments otherwise you might end up with nodes joining the wrong cluster
- For instance you could use logging-dev, logging-stage and logging-prod for the development, staging, and production clusters

```
cluster:
  name: <NAME OF YOUR CLUSTER>
```

`cluster.name: elasticsearch_netmon_prod`


**Node Name**

- You may also want to change the default node name for each node to something like the display hostname 
- By default Elasticsearch will randomly pick a Marvel character name from a list of around 3000 names when your node starts up if you haven't choosen a name

```
node:
  name: <NAME OF YOUR NODE>
```


- The hostname of the machine is provided in the environment variable `HOSTNAME`
- If on your machine you only run a single Elasticsearch node for that cluster you can set the node name to the hostname using the `${...}`` notation

```
node:
  name: ${HOSTNAME}
```

`node.name: elasticnetstack-6`


**Node Rack**

- This is used to specify special attributes like the location of the rack where the node is located
- It also appears in the JSON for Elasticsearch

```
node.rack: r1
```


**Paths**

- In production use you will almost certainly want to change paths for data and log files

```
path:
  logs: /var/log/elasticsearch
  data: /var/data/elasticsearch
  conf: /usr/local/etc/elasticsearch
```


**Memory**

- This is one of the key components of Elasticsearch
- Most operating systems try to use as much memory as possible for file system caches and eagerly swap out unused application memory, possibly resulting in the Elasticsearch process being swapped
- Swapping is very bad for performance and for node stability, so it should be avoided at all costs

- Disable swap
    + The simplest option is to completely disable swap
    + Usually Elasticsearch is the only service running on a box and its memory usage is controlled by the ES_HEAP_SIZE environment variable. There should be no need to have swap enabled
    + On Linux systems, you can disable swap temporarily by running: `sudo swapoff -a`
    + To disable it permanently, you will need to edit the `/etc/fstab` file and comment out any lines that contain the word `swap`
    
- Configure swappiness
    + The second option is to ensure that the sysctl value vm.swappiness is set to 0. This reduces the kernel’s tendency to swap and should not lead to swapping under normal circumstances while still allowing the whole system to swap in emergency conditions
    
> From kernel version 3.5-rc1 and above a swappiness of 0 will cause the OOM killer to kill the process instead of allowing swapping. You will need to set swappiness to 1 to still allow swapping in emergencies

- mlockall
    + The third option is to use `mlockall` on Linux/Unix systems preventing any Elasticsearch memory from being swapped out 

```
bootstrap.mlockall: true
```

> mlockall might cause the JVM or shell session to exit if it tries to allocate more memory than is available!

`curl http://localhost:9200/_nodes/process?pretty`

- The most probable reason, on Linux/Unix systems, is that the user running Elasticsearch doesn’t have permission to lock memory
- This can be granted by running `ulimit -l unlimited` as root before starting Elasticsearch


**Network**

- Elasticsearch binds to localhost only by default
- Set the bind address to a specific IP (IPv4 or IPv6)
- This is one of the most simple and best way to protect the data accessing by other users from elasticsearch
- You can use an IP address, hostname or an array of any combination of these

```
network.host: 127.0.0.1
```

- You can also specify a custom port based on your use case

```
http.port: 9200
```


**Discovery**

- Elasticsearch is a peer to peer based system, nodes communicate with one another directly if operations are delegated / broadcast
- In order to join a cluster, a node needs to know the hostname or IP address of at least some of the other nodes in the cluster
- This setting provides the initial list of other nodes that this node will try to contact, it accepts IP addresses or hostnames
- Defaults to `["127.0.0.1", "[::1]"]`

```
discovery.zen.ping.unicast.hosts: ["host1", "host2"]
```

- All the main APIs (index, delete, search) do not communicate with the master node
- The responsibility of the master node is to maintain the global cluster state and act if nodes join or leave the cluster by reassigning shards
- Each time a cluster state is changed the state is made known to the other nodes in the cluster
- To prevent "split brain" by configuring as the smallest possible majority of nodes to be master (total number of (nodes / 2 )+ 1)

```
discovery.zen.minimum_master_nodes: 3
```

- Example configuration

```
discovery.zen.minimum_master_nodes: 5
discovery.zen.ping.unicast.hosts: 10.64.14.66,10.64.3.249,10.9.248.64,100.64.15.128,10.9.240.151,10.9.212.223,10.65.9.238,10.65.3.43
discovery.zen.ping.multicast.enabled: false
```


**Gateway**

- The local gateway module stores the cluster state and shard data across full cluster restarts
- This must be set on every data node in the cluster

```
gateway.recover_after_nodes: 3
```

> These settings only take effect on a full cluster restart


**Various-Other**

- Disable starting multiple nodes on a single system

```
node.max_local_storage_nodes: 1
```

- Require explicit names when deleting indexes
    - This prevents accidental deletion since explicit names are now required

```
action.destructive_requires_name: true
```

- To setup master and data nodes
    - This is helpful when you are building a cluster for production

```
node.master: true
node.data: true
```

- Index related configuration changes
    - To predefine each index shard, by default elasticsearch creates 5 primary shards

```
index.number_of_shards: 5
```

- This setting is to define the shard replica
    - One of the most important configuration settings for High Availability environments
    - This can be used to take a backup within elasticsearch using the shard functionality

```
index.number_of_replicas: 1
```

- This is to dynamically auto update the elastic search indexes
    - Which helps in performance best practice

```
index.mapper.dynamic: true
```

- This is a default setting in elasticsearch which helps to create an index automatically based on the logstash output

```
action.auto_create_index: true
```

- This disables the ability to delete all indexes at a single time

```
action.disable_delete_all_indices: true
```


### Example configuration samples

https://gist.github.com/zsprackett/8546403

https://joinup.ec.europa.eu/svn/opencities/tags/opencities-core/release-3.4.3/core-module/src/main/resources/elasticsearch.yml

### References 

https://www.elastic.co/guide/en/elasticsearch/reference/current/setup-configuration.html