---

copyright:
  years: 2026
lastupdated: "2026-06-11"

keywords: HA, DR, high availability, disaster recovery, disaster recovery plan, disaster event, redis

subcollection: databases-for-redis-gen2

---

{{site.data.keyword.attribute-definition-list}}

# Understanding high availability and disaster recovery for {{site.data.keyword.databases-for-redis}}
{: #redis-ha-dr}

[Gen 2]{: tag-purple}

[High availability](#x2284708){: term} (HA) is the ability for a service to remain operational and accessible in the presence of unexpected failures. [Disaster recovery](#x2113280){: term} is the process of recovering the service instance to a working state.
{: shortdesc}

{{site.data.keyword.databases-for-redis}} is a regional service that fulfills the defined [Service Level Objectives (SLO)](/docs/resiliency?topic=resiliency-slo) with the Standard plan.

For more information about the available {{site.data.keyword.cloud_notm}} regions and data centers for {{site.data.keyword.databases-for-redis}}, see [Service and infrastructure availability by location](/docs/overview?topic=overview-services_region).

## High availability architecture
{: #ha-architecture}

![Redis architecture](/images/Redis_high_availability.svg){: caption="Redis high availability architecture" caption-side="bottom"}

{{site.data.keyword.databases-for-redis}} provides replication, failover, and high-availability features to protect your databases and data from infrastructure maintenance, upgrades, and some failures. Deployments contain two Redis data members in a primary and replica configuration. The replica is kept up to date by using asynchronous replication. High availability is monitored and coordinated by three [Redis Sentinels](https://redis.io/docs/latest/operate/oss_and_stack/management/sentinel/){: external} instances that are distributed across the deployment topology.

In a typical multi-zone deployment, the topology spans three worker nodes or availability zones:

- Zone A: primary member and one Sentinel instance (colocated)
- Zone B: replica member and one Sentinel instance (colocated)
- Zone C: one dedicated Sentinel instance (isolated)

This 3-node Sentinel layout helps maintain quorum during a single node or zone failure and enables automatic failover without manual intervention. The two colocated Sentinels (in Zones A and B) run alongside the Redis data members and the third Sentinel runs in isolation (Zone C) to ensure quorum can be maintained even if one zone fails completely.

By default, data persistence is enabled on all deployments and your data is written to disk. {{site.data.keyword.databases-for-redis}} uses a combination of [RDB snapshots and AOF (Append Only File)](https://redis.io/docs/latest/operate/oss_and_stack/management/persistence/){: external} to persist data to disk. The interval for {{site.data.keyword.databases-for-redis}} to write to disk (fsync) is set to [once every second](https://redis.io/docs/latest/operate/oss_and_stack/management/persistence/#how-durable-is-the-append-only-file){: external} to balance durability and performance.

You can turn off data persistence, which is useful for [configuring Redis as a cache](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-redis-cache).

### High availability features
{: #ha-features}

{{site.data.keyword.databases-for-redis}} supports the following high availability features:

| Feature | Description | Consideration |
| -------------- | -------------- | -------------- |
| Automatic failover | Standard on all clusters and resilient against a zone or single member failure. Sentinel instances detect failures, form quorum, and promote the replica automatically. | Recovery time depends on failure detection and promotion timing. Expect a brief interruption while clients reconnect. |
| Member count | Minimum of 2 members. Default is a Standard two-member cluster in a primary and replica configuration. A two-member cluster can recover from a single instance or zone failure, subject to replication lag. | Three Sentinel nodes monitor the health of the cluster and coordinate failovers. |
| Sentinel quorum | Three Sentinel instances are deployed (two colocated with Redis members, one isolated) so that a 2/3 quorum can survive a single worker or zone failure and authorize failover. | Client applications must tolerate a short interruption while topology changes propagate. |
| Asynchronous replication | Enables replication from primary to replica without blocking the write path, ensuring high availability with low latency. Refer to [Asynchronous replication](https://redis.io/docs/latest/operate/oss_and_stack/management/replication/){: external}. | Might result in data loss during failover due to replication lag (RPO > 0). Not suitable where strict data durability is required. |
{: caption="High availability features" caption-side="top"}

#### Asynchronous replication for {{site.data.keyword.databases-for-redis}}
{: #ha-asynchronous-replication}

By default, {{site.data.keyword.databases-for-redis}} uses asynchronous replication, where the primary node does not wait for the replica to acknowledge writes. This ensures low latency and high throughput, making {{site.data.keyword.databases-for-redis}} ideal for caching and performance-sensitive workloads. However, in the event of a primary failure, replication lag can lead to data loss, as the replica might not have received the most recent writes.

{{site.data.keyword.databases-for-redis}} replication is designed for high availability, not strict durability. A failover is automatically triggered if the primary becomes unreachable, promoting the replica to leader. Because replication is asynchronous, some committed writes may be lost during this process. This replication lag defines the Recovery Point Objective (RPO) of {{site.data.keyword.databases-for-redis}} deployments.

To reduce data loss risk, {{site.data.keyword.databases-for-redis}} supports persistence mechanisms like [RDB snapshots and AOF (Append Only File)](https://redis.io/docs/latest/operate/oss_and_stack/management/persistence/){: external}, which write data to disk independently of the replication process. These should be configured carefully based on workload requirements.

Asynchronous replication in {{site.data.keyword.databases-for-redis}} ensures fast performance but does not eliminate the possibility of data loss during failover events. It is recommended for workloads where speed and availability outweigh strict data consistency.
{: note}

### Automatic failover sequence
{: #ha-failover-sequence}

When the primary becomes unavailable, Sentinel instances monitor the loss of heartbeats, establish quorum, and coordinate failover. The replica with the best replication state is promoted to primary, the deployment is reconfigured, and clients can reconnect to the new topology. In most cases, failover completes within 30 - 90 seconds.

Applications should use client behavior that supports reconnect and topology refresh after failover. Where supported by your client library, use Sentinel-aware connection handling or implement retry logic that can tolerate a primary change.
{: important}

## Disaster recovery architecture
{: #disaster-recovery-intro}

The general strategy for disaster recovery is to create a new database, like the `Restore` database below. The contents of the new database can be a backup of the source database created before the disaster.

![Redis disaster recovery architecture](/images/Redis_disaster_recovery.svg){: caption="Redis disaster recovery architecture" caption-side="bottom"}

### Disaster recovery features
{: #dr-features}

{{site.data.keyword.databases-for-redis}} supports the following disaster recovery features:

| Feature | Description | Consideration |
| -------------- | -------------- | -------------- |
| Backup restore | Create database from previously created backup; see [Managing Cloud Databases backups](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-dashboard-backups). | New connection strings for the restored database must be referenced throughout the workload. |
{: caption="Disaster recovery features" caption-side="top"}

### Planning for disaster recovery
{: #features-for-disaster-recovery}

The disaster recovery steps must be practiced regularly. As you build your plan, consider the following failure scenarios and resolutions.

| Failure | Resolution |
| -------------- | -------------- |
| Hardware failure (single point) | {{site.data.keyword.IBM_notm}} provides a database that is resilient from single point of hardware failure within a zone. No customer configuration required. |
| Zone failure | Automatic failover. The database members are distributed between zones. |
| Data corruption | Backup restore. Use the restored database in production or for source data to correct the corruption in the restored database. |
{: caption="Failure scenarios and resolutions" caption-side="top"}

## Application-level high-availability
{: #application-level-ha}

Applications that communicate over networks and cloud services are subject to transient connection failures. Design your applications to retry connections when errors are caused by a temporary loss in connectivity to your deployment or to {{site.data.keyword.cloud_notm}}.

Because {{site.data.keyword.databases-for-redis}} is a managed service, regular updates and database maintenance occur as part of normal operations. This can occasionally cause short intervals where your database is unavailable. It can also cause the database to trigger a graceful failover, retry, and reconnect. It takes a short time for the database to determine which member is the replica and which is the primary, so you might also see a short connection interruption. Depending on failure detection and promotion timing, failovers generally complete within 30 - 90 seconds.

Your applications must be designed to handle temporary interruptions to the database, implement error handling for failed database commands, and implement retry logic to recover from a temporary interruption. Use IOREDIS, NODEREDIS or any other package of your choice to ensure continuity of your application. For more information, see [Error detection and handling with Redis blog post](https://developer.ibm.com/articles/error-detection-and-handling-with-redis){: external}.

Several minutes of database unavailability or connection interruption are not expected. Open a [support case](https://cloud.ibm.com/unifiedsupport/cases/add) with details if you have periods longer than a minute with no connectivity so we can investigate.

## Connection limits
{: #connection-limits-ha}

{{site.data.keyword.databases-for-redis}} sets a maximum of 10,000 concurrent connections per deployment. This limit ensures performance stability and resource management within your Redis environment. However, not all 10,000 connections are available to clients — a portion is reserved internally for operations that maintain the state and integrity of the deployment. After reaching the connection limit, any attempts at starting a new connection result in an error. For more information, see [Managing Redis connections](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-managing-redis-connections).

## Your responsibilities for HA and DR
{: #feature-responsibilities}

The following information can help you create and continuously practice your plan for HA and DR.

When restoring a database from backups or using point-in-time restore, a new database is created with new connection strings. Existing workloads and processes must be adjusted to consume the new connection strings.

A recovered database might also need the same customer-created dependencies of the disaster database. Ensure that this and other services exist in the recovered region:

- {{site.data.keyword.keymanagementservicefull}}

Remember that deleting a database also deletes its associated backups. However, deleted databases might be recoverable within a limited timeframe. For more information, see [Backups FAQ](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-faq-backups).

It is not possible to copy backups off the {{site.data.keyword.cloud_notm}}, so consider using the database-specific tools for additional backups. It might be required to recover from malicious database deletion followed by a reclamation-delete of a database. Careful management of IAM access to databases can help reduce exposure to this problem.

The following checklist associated with each feature can help you create and practice your plan.

- Backup restore
   - Verify that backups are available at the desired frequency to meet RPO requirements. [Managing Cloud Databases backups](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-dashboard-backups) documents backup frequency.
   - There are some restrictions on database restore regions. Verify that your restore goals can be achieved by reading [Managing Cloud Databases backups](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-dashboard-backups).
   - Verify that the retention period of the backups meet your requirements.
   - Schedule test restores regularly to verify that the actual restored times meet the defined RTO. Remember that database size significantly impacts restore time. Consider strategies to minimize restore times, such as breaking down large databases into smaller, more manageable units and purging unused data.
   - Verify the Key Protect service.

To find out more about responsibility ownership between the customer and {{site.data.keyword.cloud_notm}} for using {{site.data.keyword.databases-for-redis}}, see [Shared responsibilities for {{site.data.keyword.databases-for}}](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-responsibilities-cloud-databases).

## Stay informed: {{site.data.keyword.IBM_notm}} notifications
{: #ibm-service-notifications}

Updates affecting customer workloads are communicated through {{site.data.keyword.cloud_notm}} notifications. To stay informed about planned maintenance, announcements, and release notes related to this service, see [Monitoring notifications and status](/docs/account?topic=account-viewing-cloud-status) page. In addition, regularly review the [Version policy](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-versioning-policy) page for the latest updates on End-of-Life versions and dates.

## Additional guidance
{: #ha_dr-guidance}

- [Understanding high availability for Cloud Databases](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-ha-dr)
- [Understanding business continuity and disaster recovery for Cloud Databases](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-bc-dr)
