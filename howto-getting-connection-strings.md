---

copyright:
  years: 2026
lastupdated: "2026-07-12"

keywords: redis, databases, connection strings

subcollection: databases-for-redis-gen2

---

{{site.data.keyword.attribute-definition-list}}

# Getting connection strings
{: #connection-strings}

[Gen 2]{: tag-purple}

The {{site.data.keyword.databases-for-redis_full}} service is provisioned with authentication enabled. You need a username, password, and connection strings to connect and issue commands.

Connection strings for your deployment are displayed on the **Endpoints** panel on the **Overview page**.

Each user on your deployment receives their own connection credentials (username/password) with role-based permissions. All users connect through the same private endpoint, but access is controlled assigned role (Manager or Writer). For more information about user creation, see [Managing users and roles](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-user-management&interface=ui).

Your connection string defaults to database `0`. However, modifying your connection to connect to a database other than `0` is supported.
{: .note}

## Getting connection strings from the UI
{: #connection-strings-ui}
{: ui}

1. In your deployment's **Overview page**, scroll down to the *Endpoints* section.
2. In the *Endpoints* section you'll see the following tabs along with the connection details:
   - **CLI** contains information for connecting to your deployment using the **redis-cli** command through a virtual private endpoint.
   - **Redis** contains information for connecting to your deployment using the Redis client.

## Getting connection strings from the CLI
{: #connection-strings-cli}
{: cli}

To get the connection string for your deployment, use the following command:

```sh
ibmcloud resource service-instance <INSTANCE_CRN> --output json
```
{: pre}

Look for the field `connection` in the output to get the connection string details.

Get more details on the command using:

```sh
ibmcloud resource service-instance --help
```
{: pre}

## Getting connection strings from the API
{: #connection-strings-api}
{: api}

To get the connection string for your deployment, use the following command:
```sh
IAM_TOKEN=$(ibmcloud iam oauth-tokens -o json | jq .iam_token -r)
curl -X GET https://resource-controller.cloud.ibm.com/v2/resource_instances/<SERVICE-INSTANCE-GUID> -H "Authorization: ${IAM_TOKEN}" | jq
```
{: pre}

The connection string is in `extensions` > `dataservices` > `connection` part of the output.

## Getting connection strings from the Terraform
{: #connection-strings-terraform}
{: terraform}

To get the connection string for your deployment, use the following command:
```terraform
data "ibm_resource_group" "group" {
  name = "<your_resource_group>"
}
data "ibm_resource_instance" "<your_instance_name>" {
  name              = "<your_instance_name>"
  location          = "us-east"
  resource_group_id = data.ibm_resource_group.group.id
  service = "databases-for-redis" 
}
output "<your_instance_name>_output" {
  value ={
    extensions = data.ibm_resource_instance.<your_instance_name>.extensions
  }
}
```
{: codeblock}

`data.ibm_resource_instance.<your_instance_name>.extensions` provides the connection string details for that instance. Refer example output below:

```terraform
<your_instance_name>_output = {
 "extensions" = tomap({
  "dataservices.$schema.version" = "1.0.0"
  "dataservices.connection.cli.arguments.#" = "1"
  "dataservices.connection.cli.arguments.0" = "-h 409ffbf2-85d7-44ea-b90d-27cbf3eb8cee.private.axd.us-east.redis.dataservices.dev.appdomain.cloud -p 6379 --user $REDISUSER -a $REDISPASS --tls --sni 409ffbf2-85d7-44ea-b90d-27cbf3eb8cee.private.axd.us-east.redis.dataservices.dev.appdomain.cloud"
  "dataservices.connection.cli.bin" = "redis-cli"
  "dataservices.connection.cli.composed.#" = "1"
  "dataservices.connection.cli.composed.0" = "redis-cli -h 409ffbf2-85d7-44ea-b90d-27cbf3eb8cee.private.axd.us-east.redis.dataservices.dev.appdomain.cloud -p 6379 --user $REDISUSER -a $REDISPASS --tls --sni 409ffbf2-85d7-44ea-b90d-27cbf3eb8cee.private.axd.us-east.redis.dataservices.dev.appdomain.cloud"
  "dataservices.connection.cli.type" = "cli"
  "dataservices.connection.redis.composed.#" = "1"
  "dataservices.connection.redis.composed.0" = "rediss://$REDISUSER:$REDISPASS@409ffbf2-85d7-44ea-b90d-27cbf3eb8cee.private.axd.us-east.redis.dataservices.dev.appdomain.cloud:6379/0"
  "dataservices.connection.redis.database" = "0"
  "dataservices.connection.redis.hosts.#" = "1"
  "dataservices.connection.redis.hosts.0.hostname" = "409ffbf2-85d7-44ea-b90d-27cbf3eb8cee.private.axd.us-east.redis.dataservices.dev.appdomain.cloud"
  "dataservices.connection.redis.hosts.0.port" = "6379"
  "dataservices.connection.redis.path" = "/0"
  "dataservices.connection.redis.port" = "6379"
  "dataservices.connection.redis.query_options.tls" = "true"
  "dataservices.connection.redis.scheme" = "rediss"
  "dataservices.connection.redis.type" = "uri"
  "dataservices.redis.cpu_count" = "4"
  "dataservices.redis.host_flavor" = "bxf.4x16"
  "dataservices.redis.members" = "2"
  "dataservices.redis.memory_gb" = "16"
  "dataservices.redis.storage_gb" = "10"
  "dataservices.redis.version" = "9.0"
  "virtual_private_endpoints.dns_domain" = "409ffbf2-85d7-44ea-b90d-27cbf3eb8cee.private.axd.us-east.redis.dataservices.dev.appdomain.cloud"
  "virtual_private_endpoints.dns_hosts.#" = "2"
  "virtual_private_endpoints.dns_hosts.0" = ""
  "virtual_private_endpoints.dns_hosts.1" = "*"
  "virtual_private_endpoints.endpoints.#" = "3"
  "virtual_private_endpoints.endpoints.0.ip_address" = "10.12.131.102"
  "virtual_private_endpoints.endpoints.0.zone" = "us-east-1"
  "virtual_private_endpoints.endpoints.1.ip_address" = "10.12.132.101"
  "virtual_private_endpoints.endpoints.1.zone" = "us-east-2"
  "virtual_private_endpoints.endpoints.2.ip_address" = "10.51.221.7"
  "virtual_private_endpoints.endpoints.2.zone" = "us-east-3"
  "virtual_private_endpoints.origin_type" = "vpc"
  "virtual_private_endpoints.ports.#" = "1"
  "virtual_private_endpoints.ports.0.port_max" = "6379"
  "virtual_private_endpoints.ports.0.port_min" = "6379"
 })
}
```
{: codeblock}


## Connection string breakdown
{: #connection-strings-breakdown}

The connection information contains details about your applications that make connections to Redis.

| Field name | Index | Description |
| ---------- | ----- | ----------- |
| `Type` | | Type of connection. The connection string provides both URI and CLI connection details. |
| `Scheme` | | Scheme for a URI. For Redis, it is "rediss" (Redis with TLS). |
| `Path` | | Path for a URI. For Redis, it is the database number. |
| `Authentication` | `Username`|The username that you use to connect. |
| `Authentication` | `Password`|A password for the user. It might be shown as `$PASSWORD`. |
| `Authentication` | `Method`|How authentication takes place; "direct" authentication is handled by the driver. |
| `Hosts` | `0` | A hostname and port to connect to. |
| `Composed` | `0` | A URI combining scheme, authentication, host, and path. |
| `Bin` | | The recommended binary to create a connection. In this case it is `redis-cli`. |
| `Arguments` | `0` | The information that is passed as arguments to the command shown in the Bin field. |
{: caption="Redis connection information" caption-side="bottom"}

`0` indicates an array containing one string entry, which contains Redis connection details.

All users on your deployment can use the connection strings to connect to Redis using a private endpoint. For more information, see [Connecting through the command-line interface (CLI)](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-connecting-cli-client) and [Connecting an external application](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-external-app).

## Next steps
{: #next-steps-connection-strings}

* [Connect an external application](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-external-app).
* [Connect an IBM Cloud application](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-ibmcloud-app).
* [Connect through the CLI](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-connecting-cli-client).
* [Manage users and roles](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-user-management).
