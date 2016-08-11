# Backing up Elasticsearch using Curator
<small>[15 minutes]</small>

---

### What is Curator ?

> Elasticsearch Curator helps you curate or manage your indices. 
> Supports a variety of actions from delete a snapshot to shard allocation routing.

<br />

> This is an additional software bundle that you install on top of Elasticsearch.

---

### Curator Installation

Curator is already installed in the virtual machine

----

<div align="left">To install curator</div>

```
sudo apt-get install python-pip -y
sudo pip install elasticsearch-curator
```

Note: There is an alternate method using apt-get install as well.
<br />
```
wget -qO - https://packages.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
```
<br />
```
echo "deb http://packages.elastic.co/curator/3/debian stable main" | sudo tee -a /etc/apt/sources.list.d/curator.list
```
<br />
```
sudo apt-get update && sudo apt-get install python-elasticsearch-curator -y
```

----

(hands on)<br />
<div align="left">Create a directory to keep all the backups</div>

```
sudo mkdir -p /var/backups/elasticsearch/
```

----

<div align="left">Change the ownership of the directory to</div>

```
sudo chown elasticsearch:elasticsearch -R /var/backups/elasticsearch/
```

----

<div align="left">Add the backups path to the elasticsearch configuration</div>

```
sudo vi /etc/elasticsearch/elasticsearch.yml
```

```
path.repo: /var/backups/elasticsearch/
```

----

<div align="left">Then restart the elasticsearch service</div>

```
sudo service elasticsearch restart
```

----

Stop the second Elasticsearch node that we created earlier

----

<div align="left">Before we create backups/restores, we need to create a repo in elasticsearch</div>
<br />
<div align="left">Run the below command to create a `backup` repository in elasticsearch</div>

```
curl -XPUT 'http://localhost:9200/_snapshot/backup' -d '{
"type": "fs",
"settings": {
"location": "/var/backups/elasticsearch/",
"compress": true
}
}'
```

----

<div align="left">To snapshot an index called `apache-logs` into a repository `backup`</div>

```
curator snapshot --name=apache_logs_snapshot --repository backup indices --prefix apache-logs
```

----

<div align="left">To see all snapshots in the `backup` repository</div>

```
curator show snapshots --repository backup
```

----

<div align="left">To restore a snapshot from curator</div>

```
curl -XPOST 'http://localhost:9200/_snapshot/backup/apache_logs_snapshot/_restore'
```

---

### Restore some sample logs for creating advanced dashboards
(hands on)

----

<div align="left">Unzip the log sample called `filebeat.tar.gz` and move the logs to backups directory</div>

```
sudo tar -xvf /home/ninja/log-samples/filebeat.tar.gz -C /var/backups/elasticsearch/
```

----

<div align="left">Check the snapshot name</div>

```
curator show snapshots --repository backup
```

----

<div align="left">Then use curator to restore the logs to the elasticsearch</div>

```
curl -XPOST 'http://localhost:9200/_snapshot/backup/filebeat_logs_snapshot/_restore'
```

---

Check the head plugin to see the updated logs from import

---

### [Alerting and Advanced Dashboards](04-alerting-and-dashboards.md)