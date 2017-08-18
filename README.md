# elasticsearch_cluster

Ansible role for 5.x Elasticsearch cluster setup for centos/Redhat and Ubuntu.

# Role Name
elasticSearch cluster

# Variables/Default for elasticsearch

 This role was built using [Default_Variables](https://github.com/opstree-ansible/elasticsearch_cluster/blob/master/defaults/main.yml).

 You can set the values of elastic search configuration according to your infrastructure.

# Basic Elasticsearch Configuration
All Elasticsearch configuration parameters are supported. This is achieved using a configuration map parameter 'es_config' which is serialized into the elasticsearch.yml file.

```
 - name: Elasticsearch with custom configuration
  hosts: localhost
  roles:
    #expand to all available parameters
    - { role: elasticsearch_cluster, es_instance_name: "node1", es_data_dirs: "/var/lib/elasticsearch", es_log_dir: "/var/log/elasticsearch", 
    es_config: {
        node.name: "node1", 
        cluster.name: "custom-cluster",
        discovery.zen.ping.unicast.hosts: "localhost",
        http.port: 9201,
        transport.tcp.port: 9301,
        node.data: false,
        node.master: true,
        } 
    }

```

` The role utilises Elasticsearch version defaults. The following should be set to ensure a successful cluster forms.

```
 es_config['http.port'] - the http port for the node
 es_config['transport.tcp.port'] - the transport port for the node
 es_config['discovery.zen.ping.unicast.hosts'] - the unicast discovery list, in the comma separated format "<host1>,<host2>" (typically the clusters dedicated masters)
 es_config['network.host'] - sets both network.bind_host and network.publish_host to the same host value. The network.bind_host setting allows to control the host different network components will bind on.
```

# Multi Node Server Installations

The application of the elasticsearch role results in the installation of a node on a host. Specifying the role multiple times for a host therefore results in the installation of multiple nodes for the host.

An example of a two server deployment is shown below. The first server holds the master and is thus declared first. Whilst not mandatory, this is recommended in any multi node cluster configuration. The second server host data nodes

```
- hosts: master
  roles:
    - role: elasticSearch_cluster
      es_etc_node_master: "true"
      es_etc_node_data: "false"


- hosts: nodes
  roles:
    - role: elasticSearch_cluster
      es_etc_node_master: "false"
      es_etc_node_data: "true"

```

# Check Cluster State

From any of your Elasticsearch servers, run this command to print the state of the cluster:
```
curl -XGET 'http://server_ip:9200/_cluster/state?pretty'
```

Run this coomand on master node for create indices.
```
curl -XPUT 'localhost:9200/customer?pretty&pretty'
```

From any of your Elasticsearch servers, run this command to print the  indices of the cluster:

```
curl -XGET 'localhost:9200/_cat/indices?v&pretty'
```


# License

BSD
