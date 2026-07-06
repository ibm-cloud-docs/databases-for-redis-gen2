---

copyright:
  years: 2026
lastupdated: "2026-06-22"

keywords: troubleshooting for Redis

subcollection: databases-for-redis-gen2

content-type: troubleshoot

---

{{site.data.keyword.attribute-definition-list}}

# Why can’t I connect to my Redis deployment?
{: #troubleshoot-connect}
{: troubleshoot}
{: support}

If you encounter errors when connecting to your {{site.data.keyword.databases-for-redis_full}} deployment, review these common causes and resolutions.
{: shortdesc}

You receive an error message or fail to connect to a {{site.data.keyword.databases-for-redis}} deployment.  If reviewing application logs, you might see errors that mention intermittent connection timeouts or unable to connect.
{: tsSymptoms}

Review the following information to troubleshoot and resolve common connectivity problems:{: .tsResolve}
* An unsecured connection is a common cause of connectivity errors. All {{site.data.keyword.databases-for-redis}} connections use TLS/SSL encryption; {{site.data.keyword.databases-for-redis}} rejects unsecured connections.  To avoid errors, make sure you configured a secure connection. Refer to [Getting started](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-getting-started&interface=ui) for an example of a secure connection.
* If you use a private endpoint, make sure that you specify connection strings that contain the private endpoint (see [Credentials for private endpoints](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-private-endpoints-gen2)) and that you followed the steps in [Connecting through private endpoints](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-private-endpoints-gen2).
* If your application log captures a short connection interruption, that behavior is expected as a normal part of operations for this managed service. You want to design your applications to retry connections when errors are caused by a temporary loss in connectivity to your deployment or to IBM Cloud. However, if you experience several minutes of connection interruption check the Cloud Status for the service. For more information, see [Application-level high availability](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-redis-ha-dr#application-level-ha).
