# grafanaStack

## What is Loki?
* Loki is Log aggregation system designed to store and query logs
* Designed to be cost effective and easy to operate
* Loki does not index full text from logs (like Elasticsearch)
* Loki only indexes lables (metadata) from logs
* Makes it more cost effective and performant
* Configuration and query langauge is similar to Prometheus

***

## Architecture of Grafana Loki & Promtail

![image](https://github.com/user-attachments/assets/45f82a8c-c197-49aa-acde-e5b9d4e61cc5)

***

## Grafana Loki Components
### Promtail:
Promtail is an important component that acts as a logging agent for Loki. Its function is to collect every log from a system, label it, and send it to Loki. Loki collects logs from local log files and the system journal.

You have to install Promtail in every system you want to collect logs, likewise, if you are using Loki on Kubernenets you have to deploy Promtail in every node as a daemonset.

### Distributor:
The Distributor is a stateless component that is responsible for handling and validating the logs received from log agents like Promtail and distributing the logs to the ingester.

When the distributor receives logs, first it validates the log if everything is according to the configuration like, valid labels, isn’t older timestamps, and the log is not too long.

Once the validation is complete the distributor distributes the logs to every ingester based on the consistent hashing scheme to make sure to distribute to every available ingester equally.

### Ingester:
The Ingester is responsible for storing and indexing the logs received from the distributor on its filesystem and transferring logs to persistent storage (long-term storage) like S3 regularly.

Ingester sets a retention policy (automatic log deletion time) according to the configuration.

It uses a time-series database to store the log in a certain structural format which eases the process of efficient querying and retiring logs.

### Ruler:
The ruler is a monitoring and alerting component of Loki its role is to record metrics and trigger alerts based on the log data it receives. It does not metrics directly but converts the log data into metrics.

Ruler monitors logs and notifies if it detects any issues, it sends notifications through Email or Slack.

### Querier:
As the name says Querier is responsible for querying logs from storage and ingesters using LogQL query language. It filters and aggregates logs according to user queries like timestamps, labels, etc.

Queriers caches the previous queries to prevent querying the same log again and again, it queries the log with the same timestamp, labels, and log message only once.

### Query Frontend:
Query Frontend is a stateless component that interacts with the user, it is responsible for handling query requests, executing queries, and visualizing logs through the Grafana dashboard.

Query Frontend splits large queries into multiple smaller queries and runs all the queries at the same time, this prevents the large queries from causing memory issues in a single query and helps in faster execution.

### Grafana:
Grafana is an open-source tool that helps in queries, visualize, and monitor logs. We can integrate Loki with Grafana and visualize the log data in the dashboard, graphs, or any other visualization format available in Grafana.

Grafana uses the query language LogQL to integrate with Grafana and also we can write LogQL queries in Grafana to filter and query logs.

### Log Storage:
Loki stores log data to increase the efficiency of querying and receiving logs. It compresses log data into chunks organizes it according to time and gives a label and timestamp to it.

Then it creates an index in a key-pair format for each chunk with their log entries with the chunk timestamp and label.

For example, think index of a chunk like an index of a book.

Chunks and Index can be stored in various backend object storages or filesystems.

Once the chunks are stored it creates a retention period for the data and automatically deleted according to the retention period.

If you are using a filesystem as storage then the default storage paths for chunks and indexes are /var/lib/loki/chunks and /var/lib/loki/index.
