---

copyright:
  years: 2026
lastupdated: "2026-11-02"

keywords: redis gen 2, release notes, updates, changes, redis 8.2

subcollection: databases-for-redis-gen2

content-type: release-note

---

{{site.data.keyword.attribute-definition-list}}

# Release notes for {{site.data.keyword.databases-for-redis}}
{: #redis-relnotes}

[Gen 2]{: tag-purple}

## 2 November 2026
{: #cloud-databases-for-redis-gen2-02nov2026}
{: release-note}

{{site.data.keyword.databases-for-redis}}  Gen 1 to Gen 2 migration is now generally available
:  Migration from Gen 1 (Classic) to Gen 2 (VPC) is now available for {{site.data.keyword.databases-for-redis}}. You can migrate by using the Migration Hub in the {{site.data.keyword.cloud_notm}} console, Terraform, the API, or the CLI. You can migrate by using backup and restore to preserve Redis data or provision a new Gen 2 instance for cache-only workloads where data can be rebuilt after cutover. For more information, see [Migrating {{site.data.keyword.databases-for-redis}} from Gen 1 to Gen 2](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-migrating-redis-gen1-to-gen2&interface=cli).

## 12 October 2026
{: #databases-for-redis-gen2-12oct2026}
{: release-note}

Context-based restrictions (CBR) support now available
:  You can now use context-based restrictions (CBR) to restrict access to {{site.data.keyword.databases-for-redis}} resources based on network context, such as IP addresses, VPCs, and {{site.data.keyword.cloud}} services. For more information, see [Managing access with context-based restrictions](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-cbr&interface=ui).

## 7 October 2026
{: #databases-for-redis-gen2-07oct2026}
{: release-note}

Enhanced Bring Your Own Key (BYOK) experience in the provisioning UI
: The provisioning experience now includes an updated encryption configuration component for customer-managed encryption keys through {{site.data.keyword.keymanagementservicefull}}. This update provides a more consistent key management experience during deployment creation. Learn more about [{{site.data.keyword.keymanagementserviceshort}} integration](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-key-protect&interface=ui) or provision a new [{{site.data.keyword.databases-for-redis}} Gen 2 deployment](https://cloud.ibm.com/databases/databases-for-redis/create) with customer-managed encryption enabled.

## 30 Sep 2026
{: #databases-for-redis-gen2-30sep2026}
{: release-note}

The {{site.data.keyword.databases-for-redis}} Gen 2 is now available in all VPC multizone regions
: You can now deploy {{site.data.keyword.databases-for-redis}} Gen 2 in all supported {{site.data.keyword.cloud}} VPC multizone regions (MZRs). This release adds support for Toronto (ca-tor), Tokyo (jp-tok), Osaka (jp-osa), and Sao Paulo (br-sao). For more information, see [Overview of Gen 1 and Gen 2](/docs/databases-for-mongodb-gen2?topic=databases-for-mongodb-gen2-overview-gen1-gen2#feature-differentiators).

## 17 Sep 2026
{: #databases-for-redis-gen2-17sep2026}
{: release-note}

The {{site.data.keyword.databases-for-redis}} Gen 2 is now available in Dallas and London
: {{site.data.keyword.databases-for-redis}} Gen 2 is now available in Dallas (us-south) and London (eu-gb). These regions are in addition to the existing regions of Montreal (ca-mon), Chennai - Airtel (in-che), Mumbai (in-mum), Frankfurt (eu-de), Washington (us-east), Madrid (eu-es), and Sydney (au-syd). For more information, see [Overview of Gen 1 and Gen 2](/docs/databases-for-mongodb-gen2?topic=databases-for-mongodb-gen2-overview-gen1-gen2#feature-differentiators).

## 10 Sep 2026
{: #databases-for-redis-gen2-10sep2026}
{: release-note}

{{site.data.keyword.databases-for-redis}} Gen 2 is available in Washington, Madrid, and Sydney
:   {{site.data.keyword.databases-for-redis}} Gen 2 is now available in Washington (us-east), Madrid (eu-es), and Sydney (au-syd). For more information, see [Overview of Gen 1 and Gen 2](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-overview-gen1-gen2#feature-differentiators).

## 6 July 2026
{: #databases-for-redis-gen2-06jul2026}
{: release-note}

{{site.data.keyword.databases-for-redis}} Gen 2 is now generally available (GA)
:   {{site.data.keyword.databases-for-redis}} Gen 2 is now available in Montreal (ca-mon), Chennai (in-che), Mumbai (in-mum), and Frankfurt (eu-de). It offers the same fully managed {{site.data.keyword.databases-for-redis}} engine on newer VPC‑based infrastructure with improved security, networking, and more performant snapshot‑based backups. Gen 2 supports Redis 8.2 with Sentinel-based high availability. Redis modules are not included in this initial release but will be added in future updates. Try {{site.data.keyword.databases-for-redis}} Gen 2 now. Learn more at [Getting started](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-getting-started&interface=ui) and [Overview of Gen 1 (Classic) and Gen 2 (VPC)](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-overview-gen1-gen2&interface=ui).
