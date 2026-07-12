---

copyright:
  years: 2026
lastupdated: "2026-07-12"

keywords: redis gui, redis, redis cloud database, redis getting started, Gen 2, sentinel

subcollection: databases-for-redis-gen2

content-type: tutorial
services:
account-plan: paid
completion-time: 30m

---

{{site.data.keyword.attribute-definition-list}}

# Getting started with {{site.data.keyword.databases-for-redis_full}}
{: #getting-started}
{: toc-content-type="tutorial"}
{: toc-completion-time="30m"}

This tutorial guides you through the steps to quickly start using {{site.data.keyword.databases-for-redis}} on the Gen 2 platform by provisioning an instance, setting up a secure connection through a VSI and VPE, and enabling logging and monitoring.

* [Before you begin](#prereqs)
* [Step 1: Provision an {{site.data.keyword.databases-for-redis}} instance](#provision_instance)
* [Step 2: Creating the `Manager` or `Writer` user](#redis_user)
* [Step 3: Create a connection](#create-connections)
* [Step 4: Connect to your database](#connect-database)
* [Step 5: Connect IBM Cloud Monitoring](#connect_monitoring)
* [Step 6: Connect IBM Cloud Activity Event Routing](#activity_tracker)
* [Next steps](#next_steps)

## Before you begin
{: #prereqs}

- You need an [{{site.data.keyword.cloud_notm}} account](https://cloud.ibm.com/registration){: external}.

## Step 1: Provision an {{site.data.keyword.databases-for-redis}} instance
{: #provision_instance}

1. Log in to the [{{site.data.keyword.cloud_notm}} console](https://cloud.ibm.com/login){: external}.
2. Click the [**{{site.data.keyword.databases-for-redis}} service**](https://cloud.ibm.com/databases/databases-for-redis/create){: external} in the [**catalog**](https://cloud.ibm.com/catalog){: external}.
Complete [these steps](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-provisioning&interface=ui) to provision a {{site.data.keyword.databases-for-redis}} instance.
4. When your instance is provisioned, click the instance name to view more information.


## Step 2: Creating the `Manager` or `Writer` user
{: #redis_user}

As part of provisioning a new instance in {{site.data.keyword.cloud_notm}}, you can use the service credential console page to create a user with different roles (`Manager` and `Writer`).

Create a user with the `Manager` or `Writer` role using the {{site.data.keyword.cloud_notm}} service credential interface using the UI or CLI. These users come with the necessary credentials to connect to and manage the instance.

For more information, see [Manage users, roles and privileges](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-user-management&interface=ui).


## Step 3: Create a connection
{: #create-connections}

The **Connect** tab in Gen 2 provides guided instructions for creating a secure connection to your {{site.data.keyword.databases-for-redis}} deployment.

Because Gen 2 supports **private endpoints only**, all connections are established through the {{site.data.keyword.cloud}} private network. [Connecting through the command-line interface (CLI)](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-connecting-cli-client&interface=ui) walks you through the required setup to connect securely from your infrastructure, such as a Virtual Server Instance (VSI), by using Virtual Private Endpoint (VPE) gateway.

This guided experience is designed to help you configure a production-ready, secure connection without exposing your database to the public internet.

The following links provide a clear overview of how a connection is established within the VPC environment:

* [Create a VPC](https://cloud.ibm.com/infrastructure/network/vpcs/) (Virtual Private Cloud): A VPC is your own isolated network within {{site.data.keyword.cloud}} where you can securely run resources.
* [Generate an SSH key](https://cloud.ibm.com/infrastructure/compute/sshKeys/): SSH keys allow you to securely connect to your virtual servers.
* [Provision a Virtual Server Instance (VSI)](https://cloud.ibm.com/infrastructure/compute/vs/): A VSI is your cloud-based server where applications and workloads run.
* [Reserve a floating IP for your VSI](https://cloud.ibm.com/infrastructure/network/floatingIPs/): A floating IP is a public IP address that lets you access your VSI from the internet.
* [Create a Virtual Private Endpoint (VPE)](https://cloud.ibm.com/infrastructure/network/endpointGateways/): A VPE provides secure, private connectivity to {{site.data.keyword.cloud_notm}} services.

## Step 4: Connect to your database
{: #connect-database}

The Redis CLI is the official command-line interface for Redis. It provides a simple way to connect, send, and receive data with Redis.

IBM Cloud Databases for Redis requires TLS/SSL-secured connections, so you must use a Redis CLI client that supports TLS encryption. The Redis CLI provides native support for secure TLS connections.

To install the Redis CLI in your VSI:

```sh
sudo apt install redis-server
```
{: pre}

This gives you redis-server and redis-cli


### Verify installation
{: #verify-installation}

```
redis-cli ping
//or
redis-cli --version
```
{: pre}

Expected output:

```
PONG
//or
redis-cli 8.2.0
```
{: pre}

Now you are good to connect to your deployment using the service credentials created in [step 2](#step-2-creating-the-manager-or-writer-user)

## Step 5: Connect {{site.data.keyword.mon_full_notm}}
{: #connect_monitoring}

You can use {{site.data.keyword.mon_full_notm}} to get operational visibility into the performance and health of your applications, services, and platforms. {{site.data.keyword.mon_full_notm}} provides administrators, DevOps teams, and developers full stack telemetry with advanced features to monitor and troubleshoot, define alerts, and design custom dashboards.

For more information about how to use {{site.data.keyword.monitoringshort}} with {{site.data.keyword.databases-for-redis}}, see [Monitoring integration](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-monitoring&interface=ui).

You cannot connect {{site.data.keyword.mon_full_notm}} by using the CLI. Use the console to complete this task. For more information, see [Monitoring integration](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-monitoring).
{: note}

## Step 6: Connect {{site.data.keyword.atracker_full_notm}}
{: #activity_tracker}

{{site.data.keyword.atracker_full}} allows you to view and audit service activity to comply with corporate policies and industry regulations. {{site.data.keyword.atracker_short}} records user-initiated activities that change the state of a service in {{site.data.keyword.cloud_notm}}. Use {{site.data.keyword.atracker_short}} to track how users and applications interact with the {{site.data.keyword.databases-for-redis}} service.

To get up and running with {{site.data.keyword.atracker_full_notm}}, see [Getting started with {{site.data.keyword.atracker_full_notm}}](/docs/atracker?topic=atracker-getting-started){: external}.

{{site.data.keyword.atracker_short}} can have only one instance per location. To view events, you must access the web UI of the {{site.data.keyword.atracker_short}} service in the same location where your service instance is available. For more information, see [Launch the web UI](/docs/cloud-logs?topic=cloud-logs-getting-started){: external}.

For more information about events specific to {{site.data.keyword.databases-for-redis}}, see [{{site.data.keyword.atracker_short}} events](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-at_events&interface=api).

Events are formatted according to the Cloud Auditing Data Federation (CADF) standard. For more information about what they include, see [CADF standard](/docs/atracker?topic=atracker-event){: external}.

You cannot connect {{site.data.keyword.atracker_short}} by using the CLI. Use the console to complete this task. For more information, see [Activity tracking events](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-at_events&interface=api).
{: note}

## Next steps
{: #next-steps}

- If you are using {{site.data.keyword.databases-for-redis}} for the first time, see the [official {{site.data.keyword.databases-for-redis}} documentation](https://redis.io/documentation){: external}.
- Connect your instance to [IBM Cloud Log Analysis](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-logging&interface=ui) and [IBM Cloud Monitoring](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-monitoring&interface=ui) for observability and alerting.
- Connect to and manage your databases and data with {{site.data.keyword.databases-for-redis}}'s CLI tool [`redis-cli`](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-connecting-cli-client).
- Looking for more tools on managing your databases? Connect to your instance with the following tools:

    - [{{site.data.keyword.cloud_notm}} CLI](/docs/cli?topic=cli-install-ibmcloud-cli){: external}
    - [{{site.data.keyword.databases-for}} CLI](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-cdb-reference){: external}
    - [{{site.data.keyword.databases-for}} API](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-api){: external}

- If you plan to use {{site.data.keyword.databases-for-redis}} for your applications, see:

    - [Connecting an external application](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-external-app)
    - [Connecting an {{site.data.keyword.cloud_notm}} application](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-ibmcloud-app)

- To ensure the stability of your applications and your databases, see:

    - [High availability](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-redis-ha-dr)
    - [Performance](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-performance&interface=ui)
