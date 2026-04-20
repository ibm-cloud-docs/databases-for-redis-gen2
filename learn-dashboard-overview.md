---

copyright:
  years: 2026
lastupdated: "2026-04-20"

keywords: deployment, crn, task, gui, api endpoint, connection strings, recent tasks, observability

subcollection: databases-for-redis-gen2

---

{{site.data.keyword.attribute-definition-list}}

# The Dashboard overview
{: #dashboard-overview}

## Overview
{: #dashboard-overview-overview}

The _Overview_ page shows you information about your {{site.data.keyword.databases-for-redis_full}} deployment. The overview includes essential identifying information.

### Deployment details
{: #dashboard-overview-deployment-details}

- **Type:** The type of database that is offered by the service, and the database version that your service uses.
- **CRN (deployment ID):** The ID is a [CRN (Cloud Resource Name)](/docs/account?topic=account-crn) that uniquely identifies the database deployment. The CRN is used to refer to the database in the API and can be used with the CLI. The _Overview_ pane shows details of your service.

### Resources
{: #dashboard-overview-resources}

The resources tile contains information and configuration options on the size and resource usage of your deployment. You can [scale disk, memory, and CPU](/docs/databases-for-redis-gen2?topic=databases-for-redis-resources-scaling) and [configure Autoscaling](/docs/databases-for-redis-gen2?topic=databases-for-redis-autoscaling).

### Recent tasks
{: #dashboard-overview-recent-tasks}

Every time that you make administrative changes to your service (such as scaling, or taking a manual backup), a task starts up. The _Recent Tasks_ panel shows the task name and progress bar for any running tasks, and a list of the most recent completed tasks. Depending on how busy your deployment is, successful tasks can be shown for 24-48 hours. Unsuccessful tasks can show for 7-8 days. Tasks can also be retrieved from the [Cloud Databases API](/apidocs/cloud-databases-api/cloud-databases-api-v5#listdeploymenttasks) and [CLI plug-in](https://cloud.ibm.com/docs/databases-cli-plugin?topic=databases-cli-plugin-cdb-reference#deployment-tasks-list). A historical record of tasks from any time period is available through the [{{site.data.keyword.atracker_full}} integration](/docs/databases-for-redis-gen2?topic=databases-for-redis-at_events).

### Observability
{: #dashboard-overview-observability}

The _Observability_ tab provides access to the IBM Cloud monitoring, logging, and event tracking integrations available for your deployment.

- [{{site.data.keyword.atracker_full}}](/docs/databases-for-redis-gen2?topic=databases-for-redis-at_events)
- [{{site.data.keyword.logs_full}}](/docs/databases-for-redis-gen2?topic=databases-for-redis-logging)
- [{{site.data.keyword.monitoringfull}}](/docs/databases-for-redis-gen2?topic=databases-for-redis-monitoring)

### Endpoints
{: #dashboard-overview-endpoints}

The _Endpoints_ pane within the _Overview_ page contains connection strings for your deployment. Each tab contains connection information that is tailored to the type of connection or the protocol that uses it. Basic information includes _hostname_, _port_, the TLS service proprietary certificate, TLS/SSL parameters, and the default database of your deployment.

Reference tables for the different connection types are available on the [Getting connection strings](/docs/databases-for-redis-gen2?topic=databases-for-redis-connection-strings) page.

Connection strings reflect whether your deployment uses public endpoints, private endpoints or both. You can configure which endpoints are available on your deployment. For more information, see [Service endpoints integration](/docs/cloud-databases?topic=cloud-databases-service-endpoints).

You can manage your {{site.data.keyword.databases-for-redis}} service through the {{site.data.keyword.databases-for}} API. For more information, see [API reference](https://cloud.ibm.com/apidocs/cloud-databases-api/cloud-databases-api-v5#introduction).

## Backup and restore
{: #dashboard-overview-backups}

The _Backups_ tab is the UI for managing your deployments backups. All of the available backups are listed with their timestamps. Click a backup to grab its ID or to restore it into a new deployment. For more information, see [Managing backups](/docs/databases-for-redis-gen2?topic=databases-for-redis-dashboard-backups).

## Settings
{: #dashboard-overview-settings}

The _Settings_ tab contains the UI for many of the tunable settings for your deployment, such as the following:

- View encryption details. Encryption at rest is enabled for all {{site.data.keyword.databases-for-redis}} deployments. If you brought your own encryption key from [Key Protect](/docs/cloud-databases?topic=cloud-databases-key-protect), the panel provides a link to your Key Protect instance and the _Encryption Key_ field has the name of the key.
- [Change the admin password](/docs/databases-for-redis-gen2?topic=databases-for-redis-user-management&interface=ui#user-management-set-admin-password-ui)
- [Implement or modify an IP allowlist](/docs/databases-for-redis-gen2?topic=databases-for-redis-allowlisting)
- [Context-based restrictions](/docs/databases-for-redis-gen2?topic=databases-for-redis-cbr)

## Service credentials
{: #dashboard-overview-service-cred}

You can generate a new set of credentials for cases where you want to manually [connect an app](/docs/databases-for-redis-gen2?topic=databases-for-redis-ibmcloud-app) or [external consumer](/docs/databases-for-redis-gen2?topic=databases-for-redis-external-app) to an IBM Cloud service. For more information, see [Adding and viewing credentials](/docs/account?topic=account-service_credentials).

## View docs
{: #dashboard-overview-view-docs}

The _View docs_ link from the `Actions` drop list opens the main documentation page for {{site.data.keyword.databases-for-redis}} in a new tab.
