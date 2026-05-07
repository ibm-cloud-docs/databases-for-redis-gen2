---

copyright:
  years: 2024
lastupdated: "2026-05-07"

keywords:

subcollection: databases-for-redis-gen2

---

{{site.data.keyword.attribute-definition-list}}



# Understanding data portability for {{site.data.keyword.databases-for-redis}}
{: #data-portability}





[Data Portability](#x2113280){: term} involves a set of tools, and procedures that enable customers to export the digital artifacts that would be needed to implement similar workload and data processing on different service providers or on-prem software. It includes procedures for copying and storing the service customer's content, including the related configuration used by the service to store and process the data, on the customer's own location.
{: shortdesc}

## Responsibilities
{: #data-portability-responsibilities}

IBM Cloud services provide interfaces and instructions to guide the customer to copy and store the service customer content, including the related configuration, on their own selected location.

The customer is then responsible for the use of the exported data and configuration for the purpose of data portability to other infrastructures.

This can involve the following:

- The planning and execution for setting up alternate infrastructure on different cloud providers or on-prem software that provide similar capabilities to the IBM services.
- The planning and execution for the porting of the required application code on the alternate infrastructure, including the adaptation of the customer's application code, deployment automation, and so on.
- The conversion of the exported data and configuration to the format required by the alternate infrastructure and adapted applications


To find out more about responsibility ownership for using {{site.data.keyword.cloud}} products between {{site.data.keyword.IBM_notm}} and the customer see [Shared responsibilities for {{site.data.keyword.cloud_notm}} products](/docs/overview?topic=overview-shared-responsibilities).



For more information about your responsibilities when using {{site.data.keyword.databases-for-redis}}, see [Shared responsibilities for {{site.data.keyword.databases-for-redis_full}}](/docs/databases-for-redis-gen2?topic=databases-for-redis-responsibilities-cloud-databases).

## Data export procedures
{: #data-portability-procedures}

{{site.data.keyword.databases-for-redis}} provides mechanisms to export your content that has been uploaded, stored, and processed by the service.

You can export data directly from a running deployment by using utilities such as [RIOT](https://redis.io/docs/latest/integrate/riot/){:external} or [Redis CLI](https://redis.io/docs/latest/develop/connect/cli/#csv-output){:external}.

You can also use backup and restore workflows as part of a migration or portability strategy. A common migration approach is to create a backup or snapshot from the source environment, store it in [{{site.data.keyword.cos_full_notm}}](/docs/cloud-object-storage?topic=cloud-object-storage-about-cloud-object-storage&cloud-object-storage-about-cloud-object-storage), restore it into a {{site.data.keyword.databases-for-redis}} deployment, and then update application connection settings.

### Migration best practices
{: #data-portability-migration-best-practices}

When planning a migration to {{site.data.keyword.databases-for-redis}}, consider the following practices:

- Assess the current environment, data size, and workload patterns before selecting a migration window.
- Test the migration flow in a staging environment and validate data integrity before production cutover.
- Update client applications to use the correct connection information and verify that retry and reconnect behavior works correctly after failover.
- Keep the source environment available for rollback until you confirm that the target deployment is stable and applications are functioning as expected.

Migration downtime depends on the size of the dataset, the export and restore method that you use, and how application cutover is handled.


## Exported data formats
{: #data-portability-data-formats}



The format of the data exported from {{site.data.keyword.databases-for-redis}} depends on the export method that you use. Native Redis tools preserve Redis-compatible data representations, while backup-based migration workflows preserve the service data in a format suitable for restore operations.

## Data ownership
{: #data-ownership}

All exported data are classified as Customer content and therefore apply to them the full customer ownership and licensing rights, as stated in [IBM Cloud Service Agreement](https://www.ibm.com/support/customer/csol/terms/?id=Z126-6304_WS&cc=de&lc=en).
