---

copyright:
  years: 2025, 2026
lastupdated: "2026-05-24"

keywords: redis gen 2, release notes, updates, changes, redis 8.2, sentinel

subcollection: databases-for-redis-gen2

content-type: release-note

---

{{site.data.keyword.attribute-definition-list}}

# Release notes for {{site.data.keyword.databases-for-redis_full}} Gen 2
{: #redis-relnotes}

Use these release notes to learn about the latest updates to {{site.data.keyword.databases-for-redis_full}} that are grouped by date. Release notes are available for a minimum of three years.
{: shortdesc}

## May 2026
{: #redisgen2-may2026}

### May 2026
{: #redis-gen2-may3026}
{: release-note}

General Availability of {{site.data.keyword.databases-for-redis}} Gen 2
:   {{site.data.keyword.databases-for-redis}} Gen 2 is now generally available. This major release introduces a modern cloud-native architecture with significant improvements over Gen 1, including Redis 8.2 support, Sentinel-based high availability with 30-90 second automatic failover, enhanced security with Redis ACLs, and improved scalability with separated control and data planes. Gen 2 offers both Shared Compute and Isolated Compute hosting models to meet diverse workload requirements.

Redis 8.2 support with full protocol compatibility
:   Gen 2 supports Redis 8.2, the latest major version, with full compatibility for both RESP2 and RESP3 protocols. This ensures seamless integration with existing Redis clients while providing access to the latest Redis features and performance improvements.

Sentinel-based high availability architecture
:   Gen 2 implements a 3-node Sentinel quorum for automatic failover, providing enterprise-grade reliability. The architecture includes 2 Redis members (Primary + Replica) and 3 Sentinel instances distributed across availability zones, ensuring automatic recovery from failures within 30-90 seconds with no manual intervention.

Enhanced security with Redis ACLs
:   Gen 2 introduces comprehensive Redis ACL support for multi-user environments with granular permission control. Users can configure command restrictions, key pattern access, and role-based access control (RBAC) with predefined roles (admin, read, write, all) and custom combinations.

Separated control and data plane architecture
:   The new architecture separates control plane operations (provisioning, configuration, credential management) from data plane workloads, enabling independent scaling, improved fault isolation, and simplified operations.

Beta release of {{site.data.keyword.databases-for-redis}} Gen 2
:   {{site.data.keyword.databases-for-redis}} Gen 2 enters public beta, introducing a next-generation Redis service with modern cloud-native architecture. The beta includes Redis 8.2 support, Sentinel-based high availability, and new hosting model options. Customers are invited to test Gen 2 and provide feedback before general availability.

Shared Compute and Isolated Compute hosting models
:   Gen 2 introduces two hosting models: Shared Compute for flexible multi-tenant deployments with fine-grained resource allocation, and Isolated Compute for secure single-tenant deployments with dedicated resources and hypervisor-level isolation. Both models support the same Redis features and high availability architecture.

Hybrid persistence configuration
:   Gen 2 supports flexible persistence options including RDB snapshots, AOF (Append-Only File), and hybrid persistence (RDB + AOF). The default configuration uses hybrid persistence with AOF fsync every second, balancing durability and performance for production workloads.

Daily automated backups to Cloud Object Storage
:   Gen 2 includes daily automated backups to IBM Cloud Object Storage with configurable retention periods (7, 14, or 30 days). Backups are encrypted with AES-256 and support cross-region replication and bring-your-own-key (BYOK) via Key Protect.

{{site.data.keyword.databases-for-redis}} Gen 2 preview program
:   Selected customers gain early access to {{site.data.keyword.databases-for-redis}} Gen 2 through a preview program. The preview includes core functionality for testing and validation, with feedback incorporated into the beta release.
