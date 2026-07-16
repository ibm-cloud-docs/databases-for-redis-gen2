---
copyright:
  years: 2026
lastupdated: "2026-07-16"

keyowrds: valkey, databases, upgrading, major versions, changing versions

subcollection: databases-for-valkey-gen2

---

{{site.data.keyword.attribute-definition-list}}

# Upgrading to a new major version
{: #upgrading}

[Gen 2]{: tag-purple}

As major database versions reach end-of-life (EOL), upgrading to the next available version ensures continued support and optimal performance. Valkey version details can be found in the [catalog](https://cloud.ibm.com/databases/databases-for-valkey-gen2/create){: external}, with the [{{site.data.keyword.databases-for}} CLI plug-in](/docs/databases-cli-plugin?topic=databases-cli-plugin-cdb-reference#deployables-show){: external}, or through the [{{site.data.keyword.databases-for}} API](https://cloud.ibm.com/apidocs/cloud-databases-api/cloud-databases-api-v5#listdeployables-permissions){: external}. 

## Simple upgrade path
{: #upgrading-simple}

Because most uses of {{site.data.keyword.databases-for-valkey}} serve as a cache where the data tends to be transient, it is best to create a new deployment. In this simple method, provision a new deployment using the latest version of {{site.data.keyword.databases-for-valkey}} and then point your application to the new deployment. This builds up the cache directly through initial use.

## How to upgrade to a new major version
{: #upgrading_major}

Prepare to run on, and then migrate to, the latest version before the EOL date. For more information, see [Versioning policy](/docs/cloud-databases?topic=cloud-databases-versioning-policy){: external}.

Rolling back versions is not supported.
{: .note}

Upgrade to the latest version of Valkey available to {{site.data.keyword.databases-for-valkey}}. Find the latest version from the catalog page, from the {{site.data.keyword.databases-for}} CLI plug-in command [`ibmcloud cdb deployables-show`](/docs/databases-cli-plugin?topic=databases-cli-plugin-cdb-reference#deployables-show){: external}, or from the {{site.data.keyword.databases-for}} API [`/deployables`](/apidocs/cloud-databases-api/cloud-databases-api-v5#listdeployables){: external} endpoint.

Upgrading is handled by [restoring a backup](/docs/cloud-databases?topic=cloud-databases-dashboard-backups){: external} of your data into a new deployment. Restoring from a backup has various advantages:

- The original database stays running and production work can be uninterrupted.
- You can test the new database out of production and act on any application incompatibilities.
- The entire process can be rerun at any point.
- A fresh restoration reduces the likelihood that unneeded artifacts of the older version of the database are carried over to the new database.

### Upgrade paths
{: #upgrading-paths}

| Current version | Major version upgrade path |
| ---- | ----- |
| {{site.data.keyword.databases-for-valkey}} V7.2 |	-> {{site.data.keyword.databases-for-valkey}} V8.2 |
{: caption="Major version upgrade paths" caption-side="bottom"}


### Upgrading using the UI
{: #upgrading-ui}
{: ui}

For new hosting models, upgrading to a new major version is currently available through the [CLI](/docs/databases-for-valkey-gen2?topic=databases-for-valkey-gen2-upgrading&interface=cli) and [API](/docs/databases-for-valkey-gen2?topic=databases-for-valkey-gen2-upgrading&interface=api).
{: note}

Upgrade to a new version when [restoring a backup](/docs/cloud-databases?topic=cloud-databases-dashboard-backups&interface=ui#restore-backup) from the **Backups and restore** tab on the *Overview* page of your deployment. Click **Restore backup** on either the overflow menu or the expanded table row of your chosen backup. This opens the restore provisioning page where you can select options for the new deployment. One of the options is the Database Version, which is auto-populated with the versions available to you. Select a version and click **Restore backup** to start the provision and restore process.

### Upgrading using the CLI
{: #upgrading-cli}
{: cli}

When you upgrade and restore from backup through the {{site.data.keyword.cloud_notm}} CLI, use the provisioning command from the resource controller.

```sh
ibmcloud resource service-instance-create <INSTANCE-NAME> <SERVICE-ID> <SERVICE-PLAN-ID> <REGION>
```
{: pre}

The parameters `service-name`, `service-id`, `service-plan-id`, and `region` are all required. You also supply the `-p` with the version and backup ID parameters in a JSON object. The new deployment is automatically sized with the same disk and memory as the source deployment at the time of the backup.

The list of backups and backup IDs for a deployment can be retrieved using the following command.

```sh
ibmcloud cdb deployment-backups-list <INSTANCE_NAME_OR_CRN> --json
```
{: pre}

Use the ID of your chosen backup as a parameter in the resource controller command as shown below.

```sh
ibmcloud resource service-instance-create example-upgrade databases-for-valkey-gen2 standard us-south \
-p \ '{
  "backup_id": "crn:v1:bluemix:public:databases-for-valkey-gen2:us-south:a/54e8ffe85dcedf470db5b5ee6ac4a8d8:1b8f53db-fc2d-4e24-8470-f82b15c71717:backup:06392e97-df90-46d8-98e8-cb67e9e0a8e6",
  "version":"7.2"
}'
```
{: pre}

### Upgrading using the API
{: #upgrading-api}
{: api}

Similar to provisioning through the API, you need to complete [the required steps to use the resource controller API](/docs/databases-for-valkey-gen2?topic=databases-for-valkey-gen2-provisioning&interface=api#provision-controller-api) before you can use it to upgrade from a backup. Then, send the API a POST request. The parameters `name`, `target`, `resource_group`, `resource_plan_id`, `backup_id`, `service_endpoints`, and `version` are all required. The new deployment has the same memory and disk allocation as the source deployment at the time of the backup.

The list of backups and backup IDs for a deployment can be retrieved using the following API request.

```sh
curl -X GET  https://api.{region}.databases.cloud.ibm.com/v5/ibm/deployments/{id}/backups  
-H 'Authorization: Bearer <token>' \
```
{: pre}

Use the ID of your chosen backup in the resource controller API request as in the following example.

```sh
curl -X POST \
  https://resource-controller.cloud.ibm.com/v2/resource_instances \
  -H 'Authorization: Bearer <token>' \
  -H 'Content-Type: application/json' \
    -d '{
    "name": "new-instance-name",
    "target": "us-south",
    "resource_group": "5g9f447903254bb58972a2f3f5a4c711",
    "resource_plan_id": "databases-for-valkey-gen2-standard",
    "parameters":{
		  "backup_id": "crn:v1:bluemix:public:databases-for-valkey-gen2:us-south:a/54e8ffe85dcedf470db5b5ee6ac4a8d8:1b8f53db-fc2d-4e24-8470-f82b15c71717:backup:06392e97-df90-46d8-98e8-cb67e9e0a8e6",
		  "service_endpoints": "private",
		  "version": "7.2"
    }
  }'
```
{: pre}

## Backing up and restoring: example
{: #backup_restore}
{: ui}

Complete the following on-demand backup and restore steps to upgrade. This example sets out the steps to upgrade to the latest version.

**To create a manual backup in the UI:**

1. Go to the **Backups and restore** tab of your {{site.data.keyword.databases-for-valkey}} instance
2. Click **Create backup**: a message is displayed that a backup is in progress, and an on-demand backup is added to the list of available backups. The on-demand backup can be seen in the overview page, in the recent tasks panel.

**To restore a backup to a new service instance:**

1. Go to the **Backups and restore** tab
2. Click on the corresponding row to expand the options for the on-demand backup that you want to restore
3. Click **Restore backup**, that will re-direct you to the restore instance page.
4. On the **Restore** page, you can modify the new instance service name, region and resource allocation values. By default, the new instance is auto-sized to the same disk and memory allocation as the source instance at the time of the backup from which you are restoring. Under **Service configuration**, select latest available version as the Database Version.
5. Click **Restore backup**.

   Keep "appendonly=yes" during backup-restoration. Switch to cache mode only after restoration completes to prevent data loss.
   {: .note}

   After the new instance finishes provisioning, your data in the backup file is restored into the new instance. The new upgraded instance can be accessed from the **Resource list**.

Do not delete the source instance while the backup is restoring. Before you delete the old instance, wait until the new instance is provisioned and the backup is restored. Deleting an instance also deletes its backups.
{: .note}

## Forced upgrade
{: #forced_upgrade}

If you choose not to upgrade using restore and upgrade, {{site.data.keyword.databases-for-valkey}} will be force upgraded to the next major version at the version end of life. 
{: .note}


## If you require data migration
{: #upgrading-req-data-migration}

For guidance about migration, see [Migrating to {{site.data.keyword.databases-for-valkey}}](/docs/databases-for-valkey-gen2?topic=databases-for-valkey-gen2-migrating).
