---

copyright:
  years: 2026
lastupdated: "2026-05-11"

keywords: redis, databases, getting started, redis gen 2, sentinel

subcollection: databases-for-redis-gen2

content-type: tutorial
services:
account-plan: paid
completion-time: 10m

---

{{site.data.keyword.attribute-definition-list}}

# Getting started with {{site.data.keyword.databases-for-redis_full}}
{: #getting-started}
{: toc-content-type="tutorial"}
{: toc-services=""}
{: toc-completion-time="10m"}

{{site.data.keyword.databases-for-redis_full}} is a managed Redis service that provides a blazingly fast, in-memory data structure store with enterprise-grade reliability. This tutorial guides you through provisioning a Redis Gen 2 instance, connecting to it, and performing basic operations.
{: shortdesc}

Redis Gen 2 delivers Redis 8.2 with Sentinel-based high availability, featuring a 3-node Sentinel quorum for automatic failover with 30-90 second recovery time.

## Before you begin
{: #prereqs}

You need an [{{site.data.keyword.cloud_notm}} account](https://cloud.ibm.com/registration/){: external}. You also need to install the [{{site.data.keyword.cloud_notm}} CLI](/docs/cli?topic=cli-getting-started){: external} and the [{{site.data.keyword.databases-for}} CLI plug-in](/docs/databases-cli-plugin?topic=databases-cli-plugin-cdb-reference){: external}.

## Provision a {{site.data.keyword.databases-for-redis}} instance
{: #provision-instance}
{: step}

Provision your Redis Gen 2 instance through the [{{site.data.keyword.cloud_notm}} catalog](https://cloud.ibm.com/databases/databases-for-redis/create){: external}.

1. Log in to your {{site.data.keyword.cloud_notm}} account.
2. Navigate to the [{{site.data.keyword.databases-for-redis}} catalog page](https://cloud.ibm.com/databases/databases-for-redis/create){: external}.
3. Enter a name for your service instance.
4. Select a region for deployment.
5. Choose your hosting model:
   - **Shared Compute**: Flexible multi-tenant offering for dynamic workloads
   - **Isolated Compute**: Secure single-tenant offering for enterprise workloads
6. Configure your resource allocation based on your requirements.
7. Select your service endpoints (public, private, or both).
8. Click **Create** to provision your instance.

Provisioning takes a few minutes. You can monitor the progress from your [Resource list](https://cloud.ibm.com/resources){: external}.

For more provisioning options, including CLI, API, and Terraform, see [Provisioning](/docs/databases-for-redis-gen2?topic=databases-for-redis-provisioning).

## Set the admin password
{: #set-admin-password}
{: step}

Before connecting to your instance, set the admin password.

From the {{site.data.keyword.cloud_notm}} CLI:

```sh
ibmcloud cdb user-password <INSTANCE_NAME> admin <NEW_PASSWORD>
```
{: pre}

Replace `<INSTANCE_NAME>` with your instance name and `<NEW_PASSWORD>` with a strong password.

Alternatively, set the password from the UI:
1. Navigate to your instance from the [Resource list](https://cloud.ibm.com/resources){: external}.
2. Select **Settings** from the left navigation.
3. Click **Change Database Admin Password**.
4. Enter and confirm your new password.
5. Click **Change Password**.

## Get your connection strings
{: #get-connection-strings}
{: step}

Retrieve your connection information to connect to your Redis instance.

From the {{site.data.keyword.cloud_notm}} CLI:

```sh
ibmcloud cdb deployment-connections <INSTANCE_NAME>
```
{: pre}

This command displays connection strings for various connection types. For this tutorial, you'll use the CLI connection string.

You can also view connection strings from the UI:
1. Navigate to your instance from the [Resource list](https://cloud.ibm.com/resources){: external}.
2. Select **Overview** from the left navigation.
3. Scroll to the **Endpoints** section to view connection information.

## Connect with redis-cli
{: #connect-redis-cli}
{: step}

Connect to your Redis instance using the redis-cli command-line tool.

If you don't have redis-cli installed, install it:

**macOS:**
```sh
brew install redis
```
{: pre}

**Ubuntu/Debian:**
```sh
sudo apt-get install redis-tools
```
{: pre}

**RHEL/CentOS:**
```sh
sudo yum install redis
```
{: pre}

Connect to your instance using the connection string from the previous step:

```sh
redis-cli -h <hostname> -p <port> -a <password> --tls --cacert <path-to-cert>
```
{: pre}

Replace the placeholders with values from your connection string:
- `<hostname>`: Your Redis hostname
- `<port>`: Your Redis port (typically 32371)
- `<password>`: Your admin password
- `<path-to-cert>`: Path to your downloaded certificate file

For detailed instructions on connecting with redis-cli, including certificate setup, see [Connecting with a CLI client](/docs/databases-for-redis-gen2?topic=databases-for-redis-connecting-cli-client).

## Perform basic Redis operations
{: #basic-operations}
{: step}

Once connected, try these basic Redis commands:

Set a key-value pair:
```redis
SET mykey "Hello Redis Gen 2"
```
{: pre}

Retrieve the value:
```redis
GET mykey
```
{: pre}

Set a key with expiration (10 seconds):
```redis
SETEX tempkey 10 "This will expire"
```
{: pre}

Check if a key exists:
```redis
EXISTS mykey
```
{: pre}

Delete a key:
```redis
DEL mykey
```
{: pre}

View all keys (use with caution in production):
```redis
KEYS *
```
{: pre}

## Verify Sentinel configuration
{: #verify-sentinel}
{: step}

{{site.data.keyword.databases-for-redis}} Gen 2 uses Redis Sentinel for high availability. You can verify the Sentinel configuration:

```redis
INFO replication
```
{: pre}

This command displays replication information, including:
- Role (master or replica)
- Connected replicas
- Replication offset

The Sentinel architecture provides automatic failover with 30-90 second recovery time in case of primary node failure.

## Next steps
{: #next-steps}

Now that you have a running Redis instance, explore these topics:

- [Managing users and roles](/docs/databases-for-redis-gen2?topic=databases-for-redis-user-management) - Create additional users with specific permissions using Redis ACLs
- [Connecting an external application](/docs/databases-for-redis-gen2?topic=databases-for-redis-external-app) - Integrate Redis with your applications using Sentinel-aware client libraries
- [High availability and disaster recovery](/docs/databases-for-redis-gen2?topic=databases-for-redis-redis-ha-dr) - Understand the HA architecture and failover process
- [Configuring Redis](/docs/databases-for-redis-gen2?topic=databases-for-redis-changing-configuration) - Tune Redis settings for your workload
- [Scaling resources](/docs/databases-for-redis-gen2?topic=databases-for-redis-resources-scaling) - Adjust CPU, memory, and disk as your needs grow
- [Monitoring](/docs/databases-for-redis-gen2?topic=databases-for-redis-monitoring) - Set up monitoring and alerts for your deployment

For production deployments, consider:
- Using [private endpoints](/docs/cloud-databases?topic=cloud-databases-service-endpoints) for enhanced security
- Implementing [Context-based restrictions](/docs/databases-for-redis-gen2?topic=databases-for-redis-cbr) to control access
- Configuring [autoscaling](/docs/databases-for-redis-gen2?topic=databases-for-redis-autoscaling) to handle variable workloads
- Setting up [backups](/docs/databases-for-redis-gen2?topic=databases-for-redis-dashboard-backups) for disaster recovery
