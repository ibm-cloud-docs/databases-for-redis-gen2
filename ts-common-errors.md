---

copyright:
  years: 2024
lastupdated: "2024-05-20"

keywords: troubleshooting for Redis, common errors

subcollection: databases-for-redis-gen2

content-type: troubleshoot

---

{{site.data.keyword.attribute-definition-list}}


# How can I avoid common {{site.data.keyword.databases-for-redis}} errors?
{: #troubleshoot-common-errors}
{: troubleshoot}
{: support}


{{site.data.keyword.databases-for-redis}} is an in-memory database. It differs from traditional persistent databases in how it accepts, processes, and stores data. The following information highlights common errors that you might experience, some reasons for those errors, and the solutions that you can apply to fix those errors.

## Error: `Connection to master lost` message
{: #error-connection-lost}

{{site.data.keyword.databases-for-redis}} works with two members (master and replica) and three sentinels. The following reasons might be the cause of the `connection to master lost` message:

a. Because of lower input/output operations per second (IOPS), master is busy and is not responding to sentinels, which returns this error message in LogDNA. 

b. Because of network latency, sentinels are unable to communicate with master.

c. Scheduled maintenance activities.

d. Minor version upgrades.


### Solutions
{: #solution-connection-lost}

Try the following solutions to fix the error:

a. Increase the disk size. 1 GB is equal to 10 IOPS.

b. Move to isolated compute for dedicated resource allocation.

c. Increase RAM size.

d. RETRY logic is required, so ensure this is implemented.

e. The replica will be promoted to master automatically after few seconds.

f. Implement `memory eviction` or `TTL` policies in your Redis instance. 

{{site.data.keyword.IBM}} {{site.data.keyword.databases-for}} does not perform any activity on the master node. If needed, all activities are completed on the replica node, which is then promoted to master. Databases are set to wait for 30 seconds to find the master node before replica promotion occurs.
{: note}


## Error: Asynchronous AOF fsync is taking too long (disk is busy?)
{: #error-fsync}

AOF is Append Only File. This is the log written to Redis persistent disk when the persistent setting is on. 

Redis uses AOF and RDB files to write its log and RDB for snapshotting.
{: note}

Some of the reasons why you might see this error are as follows:

a. Because of insufficient IOPS, Redis is unable to write to disk.

b. There could be a {{site.data.keyword.databases-for-redis}} backup running at the same time as your batch is running.

c. Your application might be putting unknown API calls to the Redis instance. 

Except PSYNC which is used for metrics, we don’t request any other API in your instance.
{: note}

### Solutions:
{: #solution-fsync}

Try the following solutions to fix the error:

a. Increase disk size. A ballpark recommendation is 30% more than the expected volume.

b. If Redis is at maximum capacity, split a single Redis instance into multiple instances and adjust your application architecture.

c. Turn off persistence if it is not required. Change to cache mode instead.

Disk size cannot be scaled down but memory can be. Because Redis is an in-memory data store, we recommend that you evaluate your ongoing data size before downsizing the memory of your instance because significant reduction can result in error. 
{: note}

## Error: Scaling an instance is stuck or taking longer than expected
{: #error-scaling}

With {{site.data.keyword.Bluemix_notm}}, you can scale or resize your instance as your data needs grow. We offer autoscaling and manual scaling from the UI, CLI, and APIs. However, take care as you resize your instance. If scaling is taking longer than expected, it could be for one of the following reasons:

a. Your instance is already in the largest instance (cores*rams), and there is no bigger cluster available to move your workload to currently. In this case, a new cluster is formed and your instance is moved, which can take a few minutes.  

b. You have reduced its memory drastically (for example, from 16 GB RAM to 10 GB RAM). However, your data store size (disk) is higher, for example, 11 GB. In these cases, there is not sufficient space in RAM to read data from disk, and formation might get stuck.

### Solutions:
{: #solution-scaling}

Try the following solutions to fix the error:

a. Redis is single threaded for its primary operations. You are expected to use fewer cores and more memory. This increases the likelihood of a cluster's availability. 

b. Increase your RAM and disk based on your I/O needs.

c. Do not reduce your RAM drastically. You are advised to decrement gradually.

Ensure there is watermark memory left for Redis to perform its inherent processes.
{: note} 

## Error: READONLY You can't write against a read-only slave
{: #error-readonly}

{{site.data.keyword.databases-for-redis}} has two nodes, master and replica. Users can connect only to master node and the replica node is used for ensuring high-availability, which is generally inaccessible to users. However, as with any remote connection, a switchover can occur where the replica is promoted to master. You can experience a momentary blip in service, and no other impact is expected if the nodes are configured correctly.

a. Because of lower IOPS, master is busy and does not respond to sentinels, which returns this error message in LogDNA.

b. Because of network latency, sentinels are unable to communicate with master.

c. Scheduled maintenance.

d. Minor version upgrades.

### Solutions:
{: #solution-readonly}

Try the following solutions to fix the error:

a. Include retry and reconnect logic in your application design. You can use libraries like `ioredis` and `noderedis`. For more details, see [error detection and handling with Redis blog post](https://developer.ibm.com/articles/error-detection-and-handling-with-redis/).

b. Certain clients have retry and reconnect logic built in. You can make use of these clients as well.

c. Retry and reconnect logic is strongly recommended for any cloud services.

 






