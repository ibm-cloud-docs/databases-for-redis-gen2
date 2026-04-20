---
copyright:
  years: 2025
lastupdated: "2026-04-20"

subcollection: databases-for-redis-gen2

---

# Configuring as persistence
{: #configuring-persistence}

Redis is recognized for its high-performance key-value database, notable for storing all data in RAM to avoid slow disk access. 
Nonetheless, this RAM-centric approach poses a risk of data loss in the event of a Redis process or host incident, given the volatile nature of RAM. 
To address this concern, Redis offers mechanisms for persisting data on disk.

## Persistence modes in Redis
{: #persistence-modes}

There are two primary persistence modes available: RDB (snapshot mode) and AOF (strong persistence). Each mode entails distinct trade-offs in terms of performance and reliability. 
Hence, selecting the appropriate persistence mode in Redis necessitates a strategic decision.

### RDB snapshot
{: #rdb-snapshot}

Redis saves snapshots of the dataset on disk in a binary file called dump.rdb. The dataset is saved every N seconds if there are at least M changes.

- Save 3600 1: Every hour if at least one key has changed.
- Save 300 100: Every 5 minutes if at least 100 keys have changed.
- Save 60 10000: Every minute if at least 10,000 keys have changed.

### AOF (Append only File)
{: #aof}

With AOF enabled, Redis persists data by logging every write operation received by the service. AOF is configured by fsync policy, ensuring data durability.

- Always: Safest but with lowest performance.
- Everysec (default): Safe with better performance.
- No: Typically relies on the operating system to decide when to perform fsync, which is usually around 30 seconds (unsafe but provides the best performance).

For more information, see [Redis persistence](https://redis.io/docs/latest/operate/oss_and_stack/management/persistence/).

## Set up persistence in {{site.data.keyword.databases-for-redis}}
{: #set-up-persistence}

In {{site.data.keyword.databases-for-redis_full}} deployment, both RDB snapshot and AOF are enabled by default on provisioning and your data is written to disk. However, users can [disable AOF](/docs/databases-for-redis-gen2?topic=databases-for-redis-redis-cache) to use {{site.data.keyword.databases-for-redis}} as a cache, which alleviates the IOPS load, resulting in better performance.

{{site.data.keyword.databases-for-redis}} operates in high-availability, wherein RDB snapshots cannot be disabled.
{: note}

1. Follow [these steps](/docs/databases-for-redis-gen2?topic=databases-for-redis-provisioning&interface=ui) to provision a {{site.data.keyword.databases-for-redis}} instance.
1. Check the persistence setting by verifying {{site.data.keyword.databases-for-redis}} configuration. Access the ICD Redis instance using Redis CLI.

  As the default, AOF is set to *yes*, so {{site.data.keyword.databases-for-redis}} is configured to take AOF persistence with fsync every second along with RDB snapshots. 

  AOF can be turned off if you want to use Redis as a cache. This can also increase database availability, as the Redis process doesn’t have to replay the transaction logs in case of a failover. For more information, see [Configuring Redis as a cache](/docs/databases-for-redis-gen2?topic=databases-for-redis-redis-cache).

### Reconfigure a Databases for Redis as a persistent setting
{: #reconfigure-redis-as-persistent}

To configure changes to a {{site.data.keyword.databases-for-redis}} instance, you must utilize either the {{site.data.keyword.cloud_notm}} CLI or API for configuring persistence, as in the following example. 

Adjust the following settings:

- Set `appendonly` to *yes* to enable AOF persistence.
- Ensure `maxmemory-policy` is set to *noeviction* to prevent key expiration.
- Set `stop-writes-on-bgsave-error` to *yes* to halt writes in case of backup errors.

CLI example

```sh
ibmcloud cdb deployment-configuration '<deployment name or CRN>' '{"configuration":{"maxmemory-policy":" noeviction", "appendonly":"yes", "stop-writes-on-bgsave-error":"yes"}}'
```
{: pre}

API example

```curl
curl -X PATCH 'https://api.{region}.databases.cloud.ibm.com/v4/ibm/deployments/{id}/configuration/schema' -H "Authorization: Bearer $APIKEY" -H "Content-Type: application/json" -d '{"configuration":{ "maxmemory-policy":""noeviction, "appendonly":"yes", "stop-writes-on-bgsave-error":"yes" } }'
```
{: pre}
