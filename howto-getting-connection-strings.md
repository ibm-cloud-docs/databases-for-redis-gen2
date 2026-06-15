---

copyright:
  years: 2026
lastupdated: "2026-06-15"

keywords: redis, databases, connection strings

subcollection: databases-for-redis-gen2

---

{{site.data.keyword.attribute-definition-list}}

# Getting connection strings
{: #connection-strings}

[Gen 2]{: tag-purple}

The {{site.data.keyword.databases-for-redis_full}} service is provisioned with authentication enabled. You need a username, password, and connection strings to connect and issue commands.

Connection strings for your deployment are displayed on the **Endpoints** panel on the **Overview page**.

Your connection string defaults to database `0`. However, modifying your connection to connect to a database other than `0` is supported.
{: .note}

## Getting connection strings from the UI
{: #connection-strings-ui}
{: ui}

Complete these steps to retrieve your {{site.data.keyword.databases-for-redis}} instance connection strings:

1. In your deployment's **Overview page**, scroll down to the *Endpoints* section.
2. In the *Endpoints* section you'll see the following tabs along with the connection details:
   - **CLI** contains information for connecting to your deployment using the **redis-cli** command through a virtual private endpoint.
   - **Redis** contains information for connecting to your deployment using the Redis client.

Each user on your deployment receives their own connection credentials (username and password) with role-based permissions. All users connect through the same private endpoint, but access is controlled by their individual credentials and assigned role (Manager or Writer).

For more information about user creation, see [Managing users and roles](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-user-management&interface=ui).

## Getting connection strings from the CLI
{: #connection-strings-cli}
{: cli}

Each user on your deployment receives their own connection credentials (username and password) with role-based permissions. All users connect through the same private endpoint, but access is controlled by their individual credentials and assigned role (Manager or Writer).

For more information about user creation, see [Managing users and roles](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-user-management&interface=cli).

## Get connection strings for specified Redis deployments
{: #connection-strings-get-deployment-cli}
{: cli}

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

Each user on your deployment receives their own connection credentials (username and password) with role-based permissions. All users connect through the same private endpoint, but access is controlled by their individual credentials and assigned role (Manager or Writer).

For more information about user creation, see [Managing users and roles](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-user-management&interface=api).

## Get connection strings for specified Redis deployments from the API
{: #connection-strings-get-deployment-api}
{: api}

```sh
IAM_TOKEN=$(ibmcloud iam oauth-tokens -o json | jq .iam_token -r)
curl -X GET resource-controller.cloud.ibm.com/v2/resource_instances/<SERVICE-INSTANCE-GUID> -H "Authorization: ${IAM_TOKEN}" | jq
```
{: pre}

The connection string is in `extensions` > `dataservices` > `connection` part of the output.

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
