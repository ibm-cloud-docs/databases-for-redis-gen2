---

copyright:
  years: 2026
lastupdated: "2026-06-23"

keywords: redis gen 2, faq, frequently asked questions, sentinel, failover, migration, redis 8.2

subcollection: databases-for-redis-gen2

content-type: faq

---

{{site.data.keyword.attribute-definition-list}}

# FAQ for {{site.data.keyword.databases-for-redis}}
{: #redis-faq}

Frequently asked questions about {{site.data.keyword.databases-for-redis_full}}. To find all FAQs for {{site.data.keyword.cloud}}, see our [FAQ library](/docs/faqs).
{: shortdesc}

## What's the difference between Redis Gen 1 and Gen 2?
{: #faq-gen1-vs-gen2}
{: faq}

Redis Gen 2 features a modern cloud-native architecture with several key improvements:
- **Architecture**: Gen 2 separates control plane and data plane operations for better scalability and fault isolation.
- **High Availability**: Gen 2 uses a 3-node Sentinel quorum for automatic failover (30-90 seconds).
- **Redis Version**: Gen 2 supports Redis 8.2 with full RESP2/RESP3 protocol compatibility.
- **Topology**: Gen 2 uses 2 Redis members (Primary + Replica) with 3 Sentinel instances distributed across availability zones.
- **Hosting Models**: Gen 2 offers both Shared Compute and Isolated Compute options.

## How does Sentinel failover work in Redis Gen 2?
{: #faq-sentinel-failover}
{: faq}

Redis Gen 2 uses a 3-node Sentinel configuration for automatic failover. When the primary node fails, Sentinels detect the failure through heartbeat monitoring. They achieve quorum (2 out of 3 Sentinels must agree), select the best replica based on replication offset and promote the replica to primary. Finally, they reconfigure the remaining replica, and notify clients.
This entire process typically completes within 30-90 seconds with no manual intervention required.

## What Redis version does Gen 2 support?
{: #faq-redis-version}
{: faq}

{{site.data.keyword.databases-for-redis}} Gen 2 supports Redis 8.2 with full protocol compatibility for both RESP2 and RESP3. The service automatically uses the latest minor version to ensure optimal performance and security. For more information about version policies, see [Database Versioning Policy](/docs/cloud-databases?topic=cloud-databases-versioning-policy).

## Can I migrate from Redis Gen 1 to Gen 2?
{: #faq-migration}
{: faq}

Yes, you can migrate from Gen 1 to Gen 2 using the backup and restore method with RDB snapshots. The process involves creating an RDB snapshot of your Gen 1 instance, uploading it to Cloud Object Storage, and restoring it to a new Gen 2 instance. You'll then need to update your applications with the new connection strings. Downtime varies from hours to days depending on your data size. For detailed migration guidance, plan for testing in a staging environment first and keep your Gen 1 instance running as a backup for 30-90 days post-migration.

## What are the RTO and RPO for Redis Gen 2?
{: #faq-rto-rpo}
{: faq}

The Recovery Time Objective (RTO) for Redis Gen 2 is 30-90 seconds for automatic Sentinel failover. The Recovery Point Objective (RPO) depends on your persistence configuration. With the default hybrid persistence (RDB + AOF with fsync every second), you may lose up to 1 second of data during a failover because of asynchronous replication. For workloads requiring stricter durability, you can configure AOF with the "always" fsync policy, though this impacts performance. For more information, see [High availability and disaster recovery](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-redis-ha-dr).

## How do I connect to Redis Gen 2 with Sentinel support?
{: #faq-sentinel-connection}
{: faq}

For optimal resilience, use Sentinel-aware client libraries that automatically discover the current primary node and handle failover events. Recommended libraries include ioredis and node-redis for Node.js, redis-py for Python, Jedis or Lettuce for Java, and go-redis for Go. Configure your client with the Sentinel endpoints rather than connecting directly to Redis nodes. For code examples and detailed instructions, see [Connecting an external application](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-external-app&interface=ui).

## What data structures does Redis Gen 2 support?
{: #faq-data-structures}
{: faq}

Redis Gen 2 supports all 9 Redis data structures:
* Strings (binary-safe values up to 512MB)
* Lists (ordered collections for queues and logs)
* Sets (unique collections for tags and membership)
* Sorted Sets (score-ordered sets for leaderboards)
* Hashes (field-value maps for objects)
* Bitmaps (bit-level operations for analytics)
* HyperLogLogs (probabilistic cardinality estimation)
* Streams (append-only logs for event sourcing)
* Geospatial (location data with radius queries).

Each structure is optimized for specific use cases.

## What persistence options are available?
{: #faq-persistence}
{: faq}

Redis Gen 2 offers three persistence options: RDB snapshots (point-in-time snapshots in compact binary format), AOF (Append-Only File with configurable fsync policies), and Hybrid persistence (RDB + AOF, recommended for production). By default, Gen 2 uses hybrid persistence with AOF fsync every second, balancing durability and performance. You can also configure Redis as a cache by disabling AOF. For more information, see [Configuring persistence](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-configuring-persistence&interface=ui).

## What are the performance characteristics of Redis Gen 2?
{: #faq-performance}
{: faq}

Redis Gen 2 delivers sub-millisecond response times with p99 latency under 1ms for simple commands and under 5ms SLA overall. Throughput exceeds 100,000 operations per second per node with I/O threading enabled. The service uses jemalloc allocator for memory optimization, active defragmentation, and efficient encodings.

Performance can be scaled vertically by adjusting CPU and memory, or horizontally through read replicas. For more information, see [Performance](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-performance).

## How are backups handled in Redis Gen 2?
{: #faq-backups}
{: faq}

Redis Gen 2 provides daily automated backups to IBM Cloud Object Storage with configurable retention periods (7, 14, or 30 days). Backups are encrypted with AES-256 and support cross-region replication. You can bring your own encryption key (BYOK) via Key Protect. Backups are taken even when Redis is configured as a cache. For more information about managing backups, see [Managing backups](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-comparison-backups).

## What security features does Redis Gen 2 provide?
{: #faq-security}
{: faq}

Redis Gen 2 includes comprehensive security features:
* Redis ACLs for multi-user support with granular command and key pattern restrictions
* IBM Cloud IAM integration for federated identity management,
* Mandatory TLS 1.2+ encryption for all connections with automatic certificate rotation
* AES-256 encryption at rest for storage volumes and backups
* BYOK support via Key Protect or Hyper Protect Crypto Services
* VPC deployment with private service endpoints
* Compliance certifications including SOC 2 Type II, ISO 27001/27017/27018, GDPR, HIPAA, and PCI DSS.

For more information, see [Security and compliance](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-security-compliance).

## Can I use Redis Gen 2 as a cache?
{: #faq-redis-cache}
{: faq}

Yes, Redis Gen 2 can be configured as a cache by disabling AOF persistence and setting an appropriate maxmemory-policy for key eviction. Common policies include allkeys-lru (evicts least recently used keys), allkeys-lfu (evicts least frequently used keys), and volatile-ttl (evicts keys with shorter TTL first).

Configuring Redis as a cache reduces IOPS load and improves performance. Daily backups are still taken even in cache mode. For detailed configuration instructions, see [Configuring Redis as a cache](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-redis-cache).

## What hosting models are available?
{: #faq-hosting-models}
{: faq}

Redis Gen 2 offers two hosting models: Shared Compute (flexible multi-tenant offering for dynamic workloads with fine-grained resource allocation) and Isolated Compute (secure single-tenant offering for enterprise workloads with dedicated resources and hypervisor-level isolation). You can choose your hosting model during provisioning and switch between models later if needed. For more information, see [Hosting models](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-isolated-compute&interface=ui).

## How do I scale my Redis Gen 2 instance?
{: #faq-scaling}
{: faq}

You can scale Redis Gen 2 instances vertically by adjusting CPU, memory, and disk resources.

For Shared Compute instances, you can configure autoscaling to automatically adjust resources based on usage thresholds.

For Isolated Compute instances, CPU and RAM autoscaling is not supported, but disk autoscaling is available.

Scaling operations are performed with minimal disruption. Monitor your resources using IBM Cloud Monitoring integration to determine when scaling is needed. For more information, see [Scaling resources](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-resources-scaling&interface=ui).

## What monitoring and observability options are available?
{: #faq-monitoring}
{: faq}

Redis Gen 2 integrates with IBM Cloud's observability services:
* IBM Cloud Monitoring for metrics (memory, disk, IOPS, connections)
* IBM Cloud Logs for application and system logs
* IBM Cloud Activity Tracker for audit logging of administrative actions.

These integrations help you monitor performance, troubleshoot issues, and maintain compliance. For more information, see [Monitoring](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-performance).
