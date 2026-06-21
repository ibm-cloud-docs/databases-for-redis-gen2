---
copyright:
  years: 2026
lastupdated: "2026-06-21"

keywords: redis, databases, configs

subcollection: databases-for-redis-gen2

---

{{site.data.keyword.attribute-definition-list}}

# Changing the Redis configuration
{: #changing-configuration}

In {{site.data.keyword.databases-for-redis_full}}, you can change some of the Redis configuration settings to tune your databases to your use-case. In a typical Redis setting, you can change the configuration from the command line by using [`CONFIG SET`](https://redis.io/commands/config-set){: external}. You can still use `CONFIG SET` on your deployment but the changes do NOT persist if there is a failover, node restart, or other event on your deployment. Changing the configuration with `CONFIG SET` can be used for testing, evaluation, and tuning purposes.

In Redis 6 and above versions, only `CONFIG GET` and `CONFIG RESETSTAT` are exposed.
{: note}

To make permanent changes to the database configuration, use the {{site.data.keyword.databases-for}} [CLI plug-in](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-cdb-reference&interface=api) or [API](/apidocs/cloud-databases-api/cloud-databases-api-v5#updatedatabaseconfiguration) to write the changes to the configuration file for your deployment.

To make a change, you send a JSON object with the settings that you want to change and their new values. For example, to set the `maxmemory-policy` setting, you would supply the following either to the CLI or to the API.

```sh
{"configuration":{"maxmemory-policy":"allkeys-lru"}}
```
{: .pre}



## Using the API
{: #using-api}
{: api}

There are two deployment-configuration endpoints, one for viewing the configuration schema and one for changing the configuration. To view the configuration schema, send a `GET` request to `/deployments/{id}/configuration/schema`.

To change the configuration, send the settings that you would like to change as a JSON object in the request body of a `PATCH` request to `/deployments/{id}/configuration`.

For more information, see the [API reference](/apidocs/cloud-databases-api/cloud-databases-api-v5#updatedatabaseconfiguration).

## Available configuration settings
{: # config-settings}

To check the current value of a setting, use [`CONFIG GET`](https://redis.io/commands/config-get){: external} from a [CLI client](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-connecting-cli-client&interface=api). You can check all of the settings by using `CONFIG GET *`.

Many of the settings are used to configure Redis as a cache. For more information, see the [Configuring Redis as a Cache](/docs-draft/databases-for-redis-gen2?topic=databases-for-redis-gen2-redis-cache) page.

| Setting | Default | Notes |
| ---------- | ----- | ----------- |
| [`maxmemory`](https://redis.io/topics/memory-optimization#memory-allocation) | 80% of your deployment's memory | Determines the amount of data that you can store in Redis. |
| [`appendonly`](https://redis.io/topics/persistence#append-only-file)| `yes` | Enables or disables Redis persistence. |
| [`maxmemory-policy`](https://redis.io/topics/lru-cache#eviction-policies) |`noeviction` | Determines eviction behavior when `maxmemory` limit is reached. |
| [`maxmemory-samples`](https://redis.io/topics/lru-cache#approximated-lru-algorithm) | `5`| Tunes LRU eviction algorithm when Redis is configured as a cache. |
| `stop-writes-on-bgsave-error` | `yes` | Redis stops accepting writes if it detects an unsuccessful backup snapshot.  |
{: caption="Configuration Settings for Redis" caption-side="top"}
