---

copyright:
  years: 2024
lastupdated: "2026-04-20"

keywords: 

subcollection: cloud-databases

---

{{site.data.keyword.attribute-definition-list}}



# Understanding data portability for {{site.data.keyword.databases-for-redis}}
{: #data-portability}





[Data Portability](#x2113280){: term} involves a set of tools, and procedures that enable customers to export the digital artifacts that would be needed to implement similar workload and data processing on different service providers or on-prem software. It includes procedures for copying and storing the service customer's content, including the related configuration used by the service to store and process the data, on customer's own location.
{: shortdesc}

## Responsibilities
{: #data-portability-responsibilities}

IBM Cloud services provide interfaces and instructions to guide the customer to copy and store the service customer content, including the related configuration, on their own selected location.

The customer then is responsible for the use of the exported data and configuration for the purpose of data portability to other infrastructures.
This can involve:

- the planning and execution for setting up alternate infrastructure on on different cloud providers or on-prem software that provide similar capabilities to the IBM services
- the planning and execution for the porting of the required application code on the alternate infrastructure, including the adaptation of customer's application code, deployment automation, etc.
- the conversion of the exported data and configuration to format required by the alternate infrastructure and adapted applications


To find out more about responsibility ownership for using {{site.data.keyword.cloud}} products between {{site.data.keyword.IBM_notm}} and customer see [Shared responsibilities for {{site.data.keyword.cloud_notm}} products](/docs/overview?topic=overview-shared-responsibilities).



For more information about your responsibilities when using {{site.data.keyword.databases-for-redis}}, see [Shared responsibilities for {{site.data.keyword.databases-for-redis_full}}](/docs/databases-for-redis-gen2?topic=databases-for-redis-responsibilities-cloud-databases).

## Data export procedures
{: #data-portability-procedures}

{{site.data.keyword.databases-for-redis}} provides mechanisms to export your content that has been uploaded, stored, and processed using the service.



All data available within the service can be exported using utilities, such as [RIOT](https://redis.io/docs/latest/integrate/riot/){:external} or [Redis CLI](https://redis.io/docs/latest/develop/connect/cli/#csv-output){:external}. 


## Exported data formats
{: #data-portability-data-formats}



The format of the data exported from the {{site.data.keyword.databases-for-redis}} will be unchanged from when it was submitted.

## Data ownership
{: #data-ownership}

All exported data are classified as Customer content and therefore apply to them the full customer ownership and licensing rights, as stated in [IBM Cloud Service Agreement](https://www.ibm.com/support/customer/csol/terms/?id=Z126-6304_WS&cc=de&lc=en).


