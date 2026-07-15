---
copyright:
  years: 2026
lastupdated: "2026-07-14"

subcollection: databases-for-redis-gen2

---

{{site.data.keyword.attribute-definition-list}}

# Configuring persistence
{: #configuring-persistence}

[Gen 2]{: tag-purple}

Redis is recognized for its high-performance key-value database, notable for storing all data in RAM to avoid slow disk access. Nonetheless, this RAM-centric approach poses a risk of data loss in the event of a Redis process or host incident, given the volatile nature of RAM. To address this concern, Redis offers mechanisms for persisting data on disk.

## Persistence modes in Redis
{: #persistence-modes}

There are two primary persistence modes available: RDB (snapshot mode) and AOF (append-only logging). Each mode entails distinct tradeoffs in terms of performance and durability. Hence, selecting the appropriate persistence mode in Redis necessitates a strategic decision.

For production workloads, {{site.data.keyword.databases-for-redis}} uses a hybrid persistence model that combines RDB snapshots with AOF. This approach balances restart speed, operational efficiency, and durability.

### RDB snapshot
{: #rdb-snapshot}

Redis saves snapshots of the dataset on disk in a binary file called `dump.rdb`. A snapshot is created if at least M key changes have occurred within the last N seconds, according to the configured save rules.

For example:

- **Save 3600 1**: Save a snapshot if at least 1 key has changed during the last 3600 seconds (1 hour).
- **Save 300 100**: Save a snapshot every 300 seconds (5 minutes) if at least 100 keys have changed.
- **Save 60 10000**: Save a snapshot every minute if at least 10 000 keys have changed.

To configure redis as RDB only (snapshot persistence), the required settings are:

```sh
appendonly no

save 3600 1
save 300 100
save 60 10000

stop-writes-on-bgsave-error yes
```
{: codeblock}

### AOF (Append only File)
{: #aof}

AOF is a persistence mechanism in redis that stores data by recording every write operation (such as `SET`, `DEL`, and `INCR`) in a log file. When redis restarts, it can rebuild the dataset by replaying the operations stored in the AOF file.

The durability and performance of AOF depend on the fsync policy, which controls how often data is flushed from memory to disk:

- **Always** Data is written and synchronized to disk after every write operation. This provides the highest level of data safety but has the greatest performance impact.
- **Everysec (default)** Data is synchronized to disk once per second. This offers a good balance between durability and performance and is the default setting.
- **No** redis does not explicitly synchronize data to disk. Instead, it relies on the operating system to perform synchronization, typically every 30 seconds. This provides the best performance but carries the highest risk of data loss if a failure occurs.

To configure redis as AOF only, the required settings are:

```
appendonly yes
appendfsync everysec

save ""
```
{: codeblock}


### RDB+AOF (Default persistence mode)
{: #aof_rdb}

By default, {{site.data.keyword.databases-for-redis}} uses AOF persistence with fsync every second in a hybrid format: an RDB snapshot serves as the preamble, followed by incremental AOF commands. This configuration provides both durability and fast restart times. RDB snapshots are generated on demand for backups and replication only, not at scheduled intervals.

To configure redis as RDB+AOF, the required settings are:

```sh
appendonly yes
appendfsync everysec

save 3600 1
save 300 100
save 60 10000

stop-writes-on-bgsave-error yes
```
{: codeblock}

### Redis persistence settings
{: #persistence-settings}

| Setting | Recommended value | Description |
| ---------|-------------------|------------ |
| `maxmemory` | 80% of host memory(Default) | Prevent redis from consuming all host memory |
| `maxmemory-policy` | allkeys-lru | Evict keys when full instead of failing writes |
| `maxmemory-samples` | 5(Default) | Number of random keys to sample for eviction |
| `appendonly` | No | Disable AOF overhead for cache |
| `stop-writes-on-bgsave-error` | No (if RDB enabled) | Prevent cache writes stopping due to snapshot failure |
| `save` | 3600 1 300 100 60 10000(Default - [non-configurable]{: tag-red}) | Enables periodic RDB snapshots |
| `appendfsync` | everysec (Default - [non-configurable]{: tag-red}) | Controls how often AOF data is flushed to disk. Applies only when `appendonly` is set to `yes`|
{: caption="redis persistence settings" caption-side="top"}

For more information, see [Redis persistence](https://redis.io/docs/latest/operate/oss_and_stack/management/persistence/){: external}.

## Setting an example persistence:
{: #persistence-example}

To configure changes to a {{site.data.keyword.databases-for-redis}} instance, you must use either the IBM Cloud CLI or API for configuring persistence, as in the following example. The default/recommended persistence mode is **RDB+AOF (both enabled)**.

Leave the **appendfsync** and **save** configuration setting to default(also non-configurable) for setting the RDB+AOF persistence.
{: note}

### Configuring the RDB+AOF persistence using the CLI
{: #redis-persistence-example-cli}

```sh
ibmcloud login --sso

ibmcloud resource service-instance-update <NAME | GUID> \
  -g <RESOURCE GROUP> -p '{
    "dataservices": {
      "redis": {
        "configuration":{
          "appendonly": "yes",
          "stop-writes-on-bgsave-error": "yes"
        }
      }
    }
  }'
```
{: codeblock}

### Configuring the RDB+AOF persistence using the API
{: #redis-persistence-example-api}

```sh
IAM_TOKEN=$(ibmcloud iam oauth-tokens -o json | jq .iam_token -r)

curl -X PATCH \
  https://resource-controller.cloud.ibm.com/v2/resource_instances/<SERVICE_INSTANCE_GUID> \
  -H "Authorization: ${IAM_TOKEN}" \
  -H 'Content-Type: application/json' \
  -d '{
    "parameters":{
      "dataservices":{
        "redis":{
          "configuration":{
            "appendonly":"yes",
            "stop-writes-on-bgsave-error": "yes"
          }
        }
      }
    }
  }'
```
{: codeblock}

## redis persistence advantages
{: #redis-persistence-advantages}

- **Improved AOF performance** Optimized write operations reduce the performance impact of AOF.
- **Efficient RDB snapshots** Faster snapshot generation with lower memory overhead.
- **Hybrid persistence** Seamlessly combines RDB and AOF for optimal durability and performance.
- **Community-driven improvements** Regular enhancements to persistence mechanisms from the open source community.
