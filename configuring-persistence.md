---
copyright:
  years: 2026
lastupdated: "2026-06-21"

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

Redis saves snapshots of the dataset on disk in a binary file called `dump.rdb`. The dataset is saved every N seconds if there are at least M changes.

- Save 3600 1: Every hour if at least one key has changed.
- Save 300 100: Every 5 minutes if at least 100 keys have changed.
- Save 60 10000: Every minute if at least 10,000 keys have changed.

### AOF (Append only File)
{: #aof}

With AOF enabled, Redis persists data by logging every write operation received by the service. AOF is configured by fsync policy, ensuring data durability.

- Always: Safest but with lowest performance.
- Everysec (default): Safe with better performance.
- No: Typically relies on the operating system to decide when to perform fsync, which is usually around 30 seconds (unsafe but provides the best performance).

For more information, see [Redis persistence](https://redis.io/docs/latest/operate/oss_and_stack/management/persistence/){: external}.

## Set up persistence in {{site.data.keyword.databases-for-redis}}
{: #set-up-persistence}

In a {{site.data.keyword.databases-for-redis_full}} deployment, both RDB snapshots and AOF are enabled by default when the deployment is provisioned, and data is written to disk. Users can [disable AOF](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-redis-cache) to use {{site.data.keyword.databases-for-redis}} as a cache, which can reduce IOPS demand and improve performance for cache-oriented workloads.

{{site.data.keyword.databases-for-redis}} operates in a high-availability configuration, so RDB snapshots cannot be disabled.
{: note}

1. Complete [these steps](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-provisioning&interface=ui) to provision a {{site.data.keyword.databases-for-redis}} instance.
2. Check the persistence setting by verifying {{site.data.keyword.databases-for-redis}} configuration. Access the Redis instance using Redis CLI.

By default, AOF is enabled with the `everysec` fsync policy. This means that {{site.data.keyword.databases-for-redis}} uses AOF persistence with fsync every second together with RDB snapshots.

AOF can be turned off if you want to use Redis as a cache. This can also reduce restart time after a failover because the Redis process does not need to replay append-only logs. For more information, see [Configuring Redis as a cache](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-redis-cache).

## Backup durability and retention
{: #backup-durability-retention}

In addition to on-node persistence, {{site.data.keyword.databases-for-redis}} backups are stored in [{{site.data.keyword.cos_full_notm}}](/docs/cloud-object-storage?topic=cloud-object-storage-about-cloud-object-storage). Backups are encrypted at rest and deployments can use customer-managed keys through [Key Protect integration](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-key-protect&interface=ui).

For backup management, restore workflows, and retention details that apply to your deployment, see [Managing backups](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-comparison-backups).

## Reconfigure persistence settings for {{site.data.keyword.databases-for-redis}}
{: #reconfigure-redis-as-persistent}

To configure persistence-related changes for a {{site.data.keyword.databases-for-redis}} deployment, use either the {{site.data.keyword.cloud_notm}} CLI or the API.

Adjust the following settings as needed:

- Set `appendonly` to `yes` to enable AOF persistence.
- Ensure `maxmemory-policy` is set to `noeviction` to prevent key eviction for persistent workloads.
- Set `stop-writes-on-bgsave-error` to `yes` to halt writes if background persistence fails.

CLI example:

```sh
ibmcloud resource service-instance-update <INSTANCE_NAME_OR_CRN> \
  --parameters '{
    "configuration": {
      "maxmemory-policy": "noeviction",
      "appendonly": "yes",
      "stop-writes-on-bgsave-error": "yes"
    }
  }'
```
{: pre}

API example:

```sh
IAM_TOKEN=$(ibmcloud iam oauth-tokens -o json | jq .iam_token -r)

curl -X PATCH \
  'https://resource-controller.cloud.ibm.com/v2/resource_instances/<INSTANCE_ID>' \
  -H "Authorization: $IAM_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "parameters": {
      "configuration": {
        "maxmemory-policy": "noeviction",
        "appendonly": "yes",
        "stop-writes-on-bgsave-error": "yes"
      }
    }
  }'
```
{: pre}
