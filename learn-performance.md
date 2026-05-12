---

copyright:
  years: 2026
lastupdated: "2026-05-12"

keywords: redis, databases

subcollection: databases-for-redis-gen2

---

{{site.data.keyword.attribute-definition-list}}

# Performance
{: #performance}

{{site.data.keyword.databases-for-redis_full}} deployments can be manually [scaled to your usage](/docs/databases-for-redis-gen2?topic=databases-for-redis-resources-scaling), or configured to [autoscale](/docs/databases-for-redis-gen2?topic=databases-for-redis-autoscaling) under certain resource conditions. If you are tuning the performance of your deployment, consider a few factors.

## Monitoring your deployment
{: #monitor-deployment}

{{site.data.keyword.databases-for-redis}} deployments offer integration with the [{{site.data.keyword.monitoringfull}} service](/docs/databases-for-redis-gen2?topic=databases-for-redis-monitoring) for monitoring resource usage on your deployment. Many of the available metrics, such as memory usage, disk usage, and IOPS, can help you configure [autoscaling](/docs/databases-for-redis-gen2?topic=databases-for-redis-autoscaling). Observing trends in your workload and configuring autoscaling to respond to them can help alleviate performance issues before your deployment becomes unstable because of resource exhaustion.

## Memory policies
{: #mem-policies}

By default, deployments are configured with a `noeviction` policy. All data is kept in memory until the `maxmemory` limit is reached and Redis returns an error if the memory limit is exceeded. The `maxmemory` is set to 80% of a data node's available memory, so your node doesn't run out of system resources.

You can scale the amount of memory to accommodate more data, and you can configure the `maxmemory` setting to tune memory usage. The [Redis documentation](https://redis.io/topics/memory-optimization#memory-allocation){: external} has some good information on memory behavior and tuning `maxmemory`.

You can also configure your deployment to use [Redis as a cache](/docs/databases-for-redis-gen2?topic=databases-for-redis-redis-cache), allowing Redis to evict data out of memory once the memory limit is reached.

## Read scaling and failover behavior
{: #read-scaling-failover}

{{site.data.keyword.databases-for-redis}} uses a primary and replica topology. Write traffic is directed to the primary member, while the replica supports high availability and can help absorb read-heavy workloads where your application design supports read distribution. During failover, the replica is promoted to primary, so client applications must tolerate a brief interruption and reconnect to the new primary.

## Disk IOPS
{: #disk-iops}

The number of Input-Output Operations per second (IOPS) is limited by the type of storage volume. Storage volumes for {{site.data.keyword.databases-for-redis}} deployments are provisioned on [Block Storage Endurance Volumes in the 10 IOPS per GB tier](/docs/BlockStorage?topic=BlockStorage-orderingBlockStorage). By default, a deployment starts with persistence enabled. It's possible for very busy databases to exceed the IOPS for the disk size, and increasing disk can alleviate a performance bottleneck.

## Performance tuning considerations
{: #performance-tuning-considerations}

When you evaluate performance, consider the following service characteristics:

- Memory pressure can affect latency, especially when eviction, fragmentation, or background persistence activity increases CPU and disk usage.
- Persistence settings can affect write performance and restart behavior. AOF generally improves durability, while cache-oriented configurations can reduce IOPS demand.
- Replica lag can increase during write-heavy bursts, which can affect read-after-write expectations on replica-based read patterns.
- Scaling CPU, memory, and disk together with monitoring data is the most effective way to maintain predictable performance over time.
