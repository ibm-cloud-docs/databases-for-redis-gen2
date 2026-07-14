---

copyright:
  years: 2026
lastupdated: "2026-07-14"

keywords: redis, databases, redis cache

subcollection: databases-for-redis-gen2

---

{{site.data.keyword.attribute-definition-list}}

# Configuring Redis as a cache
{: #redis-cache}

[Gen 2]{: tag-purple}

{{site.data.keyword.databases-for-redis_full}} supports changing the [Redis database configuration](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-changing-configuration&interface=cli) and you can use it to configure [Redis as a cache](https://redis.io/topics/lru-cache){: .external}. When configured as a cache, Redis evicts old data in favor of new data according to the cache settings you define. Even when configured as a cache, {{site.data.keyword.databases-for-redis}} deployments still take a daily backup snapshot. It is not possible to disable backups on your deployment.

## Cache settings
{: #redis-cache-settings}

To configure Redis as a cache, you adjust the `maxmemory` and `maxmemory-policy` settings of your deployment. `maxmemory` defines the maximum amount of memory that the cache can use and `maxmemory-policy` defines the eviction policy applied when the `maxmemory` limit is reached. In addition, you can configure other settings for persistence, database operations, and performance tuning.

### `maxmemory`
{: #redis-cache-maxmemory}

By default, `maxmemory` is set to 80% of a data node's available memory, so your node doesn't run out of system resources. You can adjust this setting, but set a reasonable limit. Otherwise, your data can take all the available memory and your deployment runs out of resources.

### `maxmemory-policy`
{: #redis-cache-maxmemory-policy}

| Policy | Behavior |
| --------- | --------- |
| `noeviction` | Does not evict keys and returns an error when the `maxmemory` limit is reached. |
| `allkeys-lfu` | Keeps frequently used keys and removes least frequently used (LFU) keys. |
| `volatile-lfu` | Removes least frequently used keys with the expire field set to true. |
| `allkeys-lru` | Evicts less recently used (LRU) keys first. |
| `volatile-lru` | Evicts less recently used (LRU) keys from the set of keys that expire first. |
| `allkeys-random` | Evicts keys randomly. |
| `volatile-random` | Evicts keys randomly from the set of keys that expire. |
| `volatile-ttl` | Evicts keys that expire, and tries to evict keys with a shorter time to live (TTL) first. |
{: caption="Available Redis eviction policies" caption-side="top"}

With an `allkeys-*` policy, the algorithm chooses keys to evict from the entire keyspace. With a `volatile-*` policy, the algorithm chooses keys only from those that have an expiration (a TTL) set. In `volatile-*` policies, if no keys with TTL exist, no eviction occurs.
{: .tip}

### Redis cache settings
{: #redis-cache-other-settings}

| Setting | Recommended value | Description |
| ---------|-------------------|------------ |
| `maxmemory` | 80% of host memory(Default) | Prevent redis from consuming all host memory |
| `maxmemory-policy` | allkeys-lru | Evict keys when full instead of failing writes |
| `maxmemory-samples` | 5(Default) | Number of random keys to sample for eviction |
| `appendonly` | No | Disable AOF overhead for cache |
| `stop-writes-on-bgsave-error` | No (if RDB enabled) | Prevent cache writes stopping due to snapshot failure |
{: caption="Redis cache settings " caption-side="top"}

## Setting an example cache
{: #redis-cache-example-cache}

You can use `CONFIG SET` directly from a Redis CLI client, but any changes made this way are temporary and are not persisted on restarts. Use the [{{site.data.keyword.databases-for}} CLI plug-in](/docs/cloud-databases?topic=cloud-databases-cdb-reference) or [API](/apidocs/cloud-databases-api/cloud-databases-api-v5#updatedatabaseconfiguration) to change your deployment's configuration file. For more information, see [Changing your Redis configuration](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-changing-configuration&interface=cli).
{: .tip}

For example, the Redis documentation recommends the `allkeys-lru` setting as a good starting place for a general-use cache. It's also fine to leave the `maxmemory` and `maxmemory-samples` at their default values.

### Configuring the cache using the CLI
{: #redis-cache-example-cache-cli}

```sh
ibmcloud login --sso

ibmcloud resource service-instance-update <NAME | GUID> \
  -g <Resource group> -p '{
    "dataservices": {
      "redis": {
        "configuration":{
          "maxmemory-policy": "allkeys-lru",
          "appendonly": "no",
          "stop-writes-on-bgsave-error": "no"
          }
        }
      }
    }'
```
{: codeblock}

### Configuring the cache using the API
{: #redis-cache-example-cache-api}

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
            "maxmemory-policy":"allkeys-lru",
            "appendonly":"no",
            "stop-writes-on-bgsave-error": "no"
          }
        }
      }
    }
  }'
```
{: codeblock}

## Redis cache performance
{: #redis-cache-performance}

Redis offers excellent cache performance with several advantages:

- **I/O threading support**: Redis can leverage multiple CPU cores for network operations, which improves throughput for cache workloads.
- **Efficient memory management**: uses optimized memory allocation to reduce overhead.
- **Active community**: regular performance enhancements from the open-source community.

For more information on optimizing Redis cache performance, see [Performance tuning](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-performance).
