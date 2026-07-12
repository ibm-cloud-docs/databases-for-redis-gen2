---
copyright:
  years: 2026
lastupdated: "2026-07-12"

keywords: redis gen 2, pricing

subcollection: databases-for-redis-gen2

---

{{site.data.keyword.attribute-definition-list}}

# Pricing
{: #pricing}

[Gen 2]{: tag-purple}

A {{site.data.keyword.databases-for-redis}} deployment consists of a highly available Redis cluster with two data members, ensuring your data is replicated across both. Pricing is based on the total resources allocated to the deployment-disk storage, RAM, virtual CPU cores, and backup storage that is calculated on an hourly prorated basis. Gen 2 instances require at least 10 GB of disk space and the smallest configuration profile offers 4 vCPU cores.

## Using the pricing calculator
{: #pricing-calc}

Templates are provided for ease of use and to provide balanced resource allocations appropriate for general purpose workloads. You can configure resource allocation according to your requirements.

For pricing estimation, use the **Add to estimate** button on the [{{site.data.keyword.databases-for-redis}}](https://cloud.ibm.com/databases/databases-for-redis/create) create page. Input your total consumption across two data members into the calculator. This is equal to the number of members because your data is replicated to all members. For example, 10 GB of disk on a 4 vCPU x 16 GB RAM profile has a total bill for 20 GB of disk and the total cost of 2 members.

## Gen 2 backups pricing
{: #pricing-backup}

Gen 2 {{site.data.keyword.databases-for}} uses a snapshot-based backup model, with pricing aligned to the size of your provisioned database storage. Snapshots differ from traditional backups because they are block-level incremental copies. Therefore, you are billed based on how much data has changed since the last snapshot, not just the total size of your database. Snapshots have a minimum size of 1 GB and are rounded up to the next full Gigabyte.

By default, {{site.data.keyword.databases-for-redis}} provides a daily backup that is stored for 30 days. These backups and any on-demand backups you make all count toward the above allocation.

Backup storage that is included:

* You receive free backup storage equal to the total provisioned disk size of your deployment.
* This includes both automated daily backups and manual (on-demand) snapshots.
* For example, if your 2 member {{site.data.keyword.databases-for-redis}} deployment is provisioned with 20 GB of disk per member, you get 40 GB of backup storage included at no cost.

Overage charges are as follows:

* The overage is billed monthly
* Total snapshot storage = day 1 full + (daily change × 29 days x number of members)
* Overage is charged at $0.03 per GB per month.

## Worked example for overage
{: #overage-example}

For a 2-member Redis deployment with 20 GB of data per member:

* Day 1: a full snapshot is taken from the current primary. This consumes 20 GB of snapshot storage.

    This models the worst case scenario where the full snapshot is equal to the file system size. In practice, especially for new databases that grow over time, snapshot sizes are typically smaller, which helps reduce backup costs.
    {: note}

* Day 2-16: you write 2 GB of new data per day. Snapshots are incremental and only store changes. Over 15 days, this adds 30 GB, which brings total snapshot usage to 20 GB + 30 GB = 50 GB.

* In this scenario, your backup storage utilization is now greater than the free allocation of 40 GB for the month. Billing incurs for an overage at a rate of $0.03/month per gigabyte.

* Day 17: a failover occurs and one secondary member becomes the new primary. A full snapshot is taken from this new primary, which consumes another 20 GB.

* Day 18-30: you continue writing 2 GB per day, adding 26 GB over 13 days.

    Total snapshot = 20 GB (initial) + 30 GB (incremental) + 20 GB (failover snapshot) + 26 GB (post-failover incremental) = 96 GB

    Free allocation = 20 GB x 2 members = 40 GB

    Overage = 96 GB - 40 GB = 56 GB

    Monthly charge = (96 GB - 40 GB) x $0.03 = $1.68

With large deployments and frequent writes, you might exceed the free tier after the first snapshot.

* Cross-region copies: if you choose to copy snapshots to another region, {{site.data.keyword.cloud}} charges for the full size of the snapshot in the destination region (not incremental) and continued incremental growth in the original region as new snapshots are taken.

    Most deployments will not ever go over the allotted credit.

## Dedicated cores pricing
{: #cores-pricing}

You have the option of selecting the CPU allocation for your deployment. With dedicated cores, your resource group is given a single-tenant host with a guaranteed minimum reserve of CPU shares. Your deployments are then allocated with the number of dedicated CPUs. For example, if the cost of dedicated cores is $30 per core per month and you provision a deployment with 4 dedicated cores per member, that is a total of 8 cores from 2 member pods. This is billed at $240 per month.

## Scaling per member
{: #scaling-member}

{{site.data.keyword.databases-for-redis}} instances have minimum and maximum allocation for disk and RAM as shown. Scaling instances through the API and CLI provides more granularity and also allows you to scale a database instance up to 4 TB of disk per member. Minimum and maximum CPU and RAM combinations vary per region and according to the host flavor, see [Isolated Compute](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-isolated-compute&interface=cli).

Each GB of disk provides 10 IOPS.
{: note}

| Resource | Minimum | Maximum | Scaling granularity (API/CLI) |
| ---------- | ----- | ----- | ------- |
| Disk | 10 GB per member | 4 TB per member | 1024 MB per member |
{: caption="Scaling limits" caption-side="top"}

The CPU and RAM are determined by the selected host flavor, not configured independently. Host size and disk allocation is for per member.

| Host Flavor | CPU Cores | RAM (GB) |
| ---------- | ----- | ----- |
| bx3d.4x20 | 4 | 20 |
| bx3d.8x40 | 8 | 40 |
{: caption="Host flavor options" caption-side="top"}
