---
copyright:
  years: 2026
lastupdated: "2026-07-14"

keywords: redis, databases, configs

subcollection: databases-for-redis-gen2

---

{{site.data.keyword.attribute-definition-list}}

# Changing the Redis configuration
{: #changing-configuration}

[Gen 2]{: tag-purple}

In {{site.data.keyword.databases-for-redis_full}}, you can change some of the Redis configuration settings to tune your databases to your use-case. In a typical Redis setting, you can change the configuration from the command line by using [`CONFIG SET`](https://redis.io/commands/config-set){: external}. You can still use `CONFIG SET` on your deployment but the changes do NOT persist if there is a failover, node restart, or other event on your deployment. Changing the configuration with `CONFIG SET` can be used for testing, evaluation, and tuning purposes.

In Redis 6 and above versions, only `CONFIG GET` and `CONFIG RESETSTAT` are exposed.
{: note}

To make permanent changes to the database configuration, use the {{site.data.keyword.databases-for}} [CLI plug-in](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-cdb-reference&interface=api) or [API](/apidocs/cloud-databases-api/cloud-databases-api-v5#updatedatabaseconfiguration) to write the changes to the configuration file for your deployment.

## Using the CLI
{: #using-cli}

```sh
ibmcloud resource service-instance-update <NAME | ID> \
 -g <resource group> -p '{
    "parameters": {
      "dataservices": {
        "redis": {
          "configuration":{
            "maxmemory-policy": "noeviction"
          }
        }
      }
    }'
```
{: codeblock}

You can view the configuation details by retrieving the service instance details.
```sh
   ibmcloud resource service-instance <INSTANCE_NAME>
```
{: codeblock}

## Using the API
{: #using-api}

```sh
IAM_TOKEN=$(ibmcloud iam oauth-tokens -o json | jq .iam_token -r)

curl -X PATCH \
  'https://resource-controller.cloud.ibm.com/v2/resource_instances/<SERVICE_INSTANCE_GUID>' \
  -H "Authorization: ${IAM_TOKEN}" \
  -H 'Content-Type: application/json' 
  -d '{"
    parameters":{
      "dataservices":{
        "redis":{
          "configuration":{
            "maxmemory-policy": "noeviction"
          }
        }
      }
    }
  }'
```
{: codeblock}
You can view the configuation details by retrieving the service instance details.
```sh
curl -X GET https://resource-controller.cloud.ibm.com/v2/resource_instances/<service-instance-id> -H "Authorization: ${IAM_TOKEN}" | jq
```
{: codeblock}

For more information, see the [API reference](/apidocs/resource-controller/resource-controller#intro).

## Available configuration settings
{: # config-settings}

To check the current value of a setting, use [`CONFIG GET`](https://redis.io/commands/config-get){: external} from a [CLI client](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-connecting-cli-client&interface=api). You can check all of the settings by using `CONFIG GET *`.

The following table details the customer configurable parameters:

| Parameter | Description | Default
| ---------- | ----- | ----------- |
| `max-memory` | Maximum memory limit for redis | 80% of host memory |
| `maxmemory-policy` | Eviction policy when max memory is reached | noeviction |
| `maxmemory-samples` | Number of samples for LRU/LFU eviction (1-10) | 5 |
| `appendonly` | Enable/disable AOF (Append Only File) persistence | Yes |
| `stop-writes-on-bgsave-error` | Stop accepting writes if RDB save fails | Yes |
{: caption="Configurable parameters for redis" caption-side="top"}

For more information on redis configuration, refer [Redis conf](https://redis.io/docs/latest/operate/oss_and_stack/management/config) and [Redis 8.2 conf](https://raw.githubusercontent.com/redis/redis/8.2/redis.conf).
