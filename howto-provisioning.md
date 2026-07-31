---

copyright:
  years: 2026
lastupdated: "2026-07-23"

keywords: provision cloud databases, terraform, provisioning parameters, cli, resource controller api, provision redis

subcollection: databases-for-redis-gen2

---

{{site.data.keyword.attribute-definition-list}}

# Provisioning
{: #provisioning}

[Gen 2]{: tag-purple}

Provision a {{site.data.keyword.databases-for-redis_full}} deployment through the [catalog](https://cloud.ibm.com/databases/databases-for-redis-gen2/create){: external}, the [{{site.data.keyword.databases-for}} CLI plug-in](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-cdb-reference){: external}, the [{{site.data.keyword.databases-for}} API](/apidocs/resource-controller/resource-controller#create-resource-instance){: external}, or using [Terraform](https://registry.terraform.io/providers/IBM-Cloud/ibm/latest/docs/resources/database){: external}.

## Provisioning through the {{site.data.keyword.cloud_notm}} console
{: #catalog}
{: ui}

Deploy from the console by specifying the following parameters.

1. In **Service details**, configure the following:
    * **Location** Select a location that supports Gen 2.
    * **Service name** The name can be any string and is the name that is used on the web and in the CLI to identify the new instance.
    * **Resource group** If you are organizing your services into resource groups. For more information, see [Managing resource groups](/docs/account?topic=account-rgs&interface=ui).
2. **Hosting model: Isolated** is a secure single-tenant offering for complex, highly performant enterprise workloads. For more information, see [Hosting models](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-isolated-compute).
3. **Resource allocation** Fine tune your resource allocation by choosing the required host size for your deployment and specify the required disk size. Host size and disk allocation is per member.

    Disk size can be increased after provisioning but cannot be decreased to prevent data loss.
    {: note}
3. In **Service configuration**, configure the following:
    * **Database version** The deployment version of your database. To ensure optimal performance, run the preferred version. The latest minor version is used automatically. For more information, see [Database versioning policy](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-versioning-policy&interface=ui).
    * **Encryption** If you use [Key Protect](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-key-protect&interface=ui), an instance and key can be selected to encrypt the instance's disk. If you do not use your own key, the instance automatically creates and manages its own disk encryption key.
    * **Endpoints** Configure the [service endpoints](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-private-endpoints-gen2) on your deployment. The default setting is _private_.
4. Click **Create**. The Cloud Databases **Resource list** page opens.
5. When your instance has been provisioned, click the instance name to view more information.

## Provisioning through the CLI
{: #use-cli}
{: cli}

Before provisioning, complete the instructions provided in the documentation to install the [{{site.data.keyword.cloud_notm}} CLI tool](/docs/cli?topic=cli-install-ibmcloud-cli){: external}.

1. Log in to {{site.data.keyword.cloud_notm}}. If you use a federated user ID, it's important that you switch to a one-time passcode (`ibmcloud login --sso`), or use an API key (`ibmcloud --apikey key or @key_file`) to authenticate. For more information about how to log in by using the CLI, see [General CLI (ibmcloud) commands](/docs/cli?topic=cli-ibmcloud_cli#ibmcloud_login){: external} under `ibmcloud login`.

   ```sh
   ibmcloud login
   ```
   {: pre}

2. Target a resource group:

   ```sh
   ibmcloud target -g Default
   ```
   {: pre}

3. Create a {{site.data.keyword.databases-for-redis}} service instance in {{site.data.keyword.cloud_notm}} by running a command like:

   ```sh
   ibmcloud resource service-instance-create <INSTANCE_NAME> <SERVICE_NAME> <SERVICE_PLAN_NAME> <LOCATION> -p '{"dataservices": {"redis": {"host_flavor":"bx3d.4x20"}}}' --user-tags test-redis,do-not-delete
   ```
   {: pre}

   For example, to provision a {{site.data.keyword.databases-for-redis}} instance, use a command like:

   ```sh
   ibmcloud resource service-instance-create redis-test-database databases-for-redis databases-for-redis-gen2-standard ca-mon -p '{"dataservices": {"redis": {"host_flavor":"bx3d.4x20"}}}' --user-tags test-redis,do-not-delete
   ```
   {: pre}

   The `host_flavor` parameter defines your Compute sizing. Input the appropriate value for your desired CPU and RAM configuration. For available host flavor values, see [host flavor size selection](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-isolated-compute&interface=cli).

   The fields in the command are described in the following table:

   | Field | Description | Flag |
   |-------|------------|------------|
   | `INSTANCE_NAME` [Required]{: tag-red} | The instance name can be any string and is the name that is used on the web and in the CLI to identify the new deployment. |  |
   | `SERVICE_NAME` [Required]{: tag-red} | Name or ID of the service. For {{site.data.keyword.databases-for-redis}}, use `databases-for-redis`. |  |
   | `SERVICE_PLAN_NAME` [Required]{: tag-red} | Standard gen2 plan (`standard-gen2`) |  |
   | `LOCATION` [Required]{: tag-red} | The location where you want to deploy. To retrieve a list of regions, use the `ibmcloud regions` command. In case of an API call, Location is referred to as target |  |
   | `RESOURCE_GROUP` | The resource group name. The default value is `Default`. Provide resource group ID in case of API call. | -g |
   | `--parameters` | JSON file or JSON string of parameters to create the service instance | -p |
   | `host_flavor` | For Isolated Compute, select desired CPU and RAM configuration. For more information, see the following table or [Hosting models](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-isolated-compute).| |
   | `--user-tags` | Comma separated list of user tag names |  |
   {: caption="Table 1. Basic command format fields" caption-side="bottom"}

   You will see a response like the following:

   ```text
   Creating service instance redis-test-database in resource group Default of account ICD as user@ibm.com...
   OK
   Service instance redis-test-database was created.

   Name:                   redis-test-database
   ID:                     crn:v1:bluemix:public:databases-for-redis:ca-mon:a/23b09aee04da4545b6e32805fa93249d:4b6b8771-b44e-4687-9393-db8a2dec1942::
   GUID:                   4b6b8771-b44e-4687-9393-db8a2dec1942
   Location:               ca-mon
   State:                  provisioning
   Type:                   service_instance
   Sub Type:
   Allow Cleanup:          false
   Locked:                 false
   One-time credentials:   false
   Created at:             2026-06-25T12:48:00Z
   Updated at:             2026-06-25T12:48:01Z
   Last Operation:
                           Status    create in progress
                           Message   Started create instance operation
   ```
   {: codeblock}

   To check provisioning status, use the following command:

   ```sh
   ibmcloud resource service-instance <INSTANCE_NAME>
   ```
   {: pre}

   When complete, you will see a response like the following:

   ```text
   Retrieving service instance INSTANCE_NAME in resource group default under account USER's Account as USER...
   OK

   Name:                  INSTANCE_NAME
   ID:                    crn:v1:bluemix:public:databases-for-redis:ca-mon:a/23b09aee04da4545b6e32805fa93249d:4b6b8771-b44e-4687-9393-db8a2dec1942::
   GUID:                  4b6b8771-b44e-4687-9393-db8a2dec1942
   Location:              <LOCATION>
   Service Name:          databases-for-redis
   Service Plan Name:     standard-gen2
   Resource Group Name:   default
   State:                 active
   Type:                  service_instance
   Sub Type:              Public
   Locked:                false
   Service Endpoints:     private
   Created at:            2026-06-25T19:42:07Z
   Created by:            USER
   Updated at:            2026-06-25T19:53:25Z
   Last Operation:
                          Status    create succeeded
                          Message   Provisioning redis with version 8.2 (100%)
   ```
   {: codeblock}

### Update a service instance
{: #update-service-instance-cli}
{: cli}

You can update the service instance name, service-plan-id, and parameters using the command like the following:

```sh
ibmcloud resource service-instance-update service-instance-current-name --service-plan-id databases-for-redis-gen2-standard -p '{"dataservices": {"redis": {"storage_gb":20}}}' -g Default --name NEW_NAME
```
{: pre}

### Deleting a service instance
{: #delete-service-instance-cli}
{: cli}

Delete an instance by running a command like the following:

```sh
ibmcloud resource service-instance-delete <INSTANCE_NAME_OR_CRN>
```
{: pre}

### Restore from Redis backup
{: #restore-backup-cli}
{: cli}

You can provision a new Redis instance from a particular backup using a command like the following:

```sh
ibmcloud resource service-instance-create restore-instance-name databases-for-redis standard-gen2 ca-mon -g Default  -p '{
      "dataservices":{
            "restore_backup_id": "<service-instance-crn>:backup:<backup-uuid>"
      }
}'
```
{: pre}

## Provisioning through the Resource Controller API
{: #provision-controller-api}
{: api}

Complete these steps to provision using the [Resource Controller API](/apidocs/resource-controller/resource-controller#create-resource-instance){: external}.

1. Obtain an IAM token using the following command:

   ```sh
   IAM_TOKEN=$(ibmcloud iam oauth-tokens -o json | jq .iam_token -r)
   ```
   {: pre}

2. You need to know the ID of the resource group that you want to deploy to. This information is available through the [{{site.data.keyword.cloud_notm}} CLI](/docs/cli?topic=cli-ibmcloud_commands_resource#ibmcloud_resource_groups).

   Use a command like:

   ```sh
   ibmcloud resource groups
   ```
   {: pre}

   Note down the desired resource group ID. In most cases we use the default resource group value.

3. You need to know the region you want to deploy to.

   To list all the regions that deployments can be provisioned into from the current region, use the [{{site.data.keyword.databases-for}} CLI plug-in](/docs/cloud-databases?topic=cloud-databases-cdb-reference#regions){: external}.

   The command looks like the following:

   ```sh
   ibmcloud cdb regions --json
   ```
   {: pre}

4. The `host_flavor` parameter defines your Compute sizing. Choose the appropriate value for your deployment. For available host flavor values, see [Gen 2 isolated compute](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-isolated-compute&interface=api).

5. When you have all the information, [provision a new resource instance](/apidocs/resource-controller/resource-controller#create-resource-instance){: external} with the {{site.data.keyword.cloud_notm}} Resource Controller.

   ```sh
   curl -X POST https://resource-controller.cloud.ibm.com/v2/resource_instances -H "Authorization: ${IAM_TOKEN}"   -H 'Content-Type: application/json' -d '{
       "name": "redis-test",
       "target": "ca-mon",
       "resource_group": "c21a4e8564c14d1aab2a9a8b441904eb",
       "resource_plan_id": "databases-for-redis-gen2-standard",
       "parameters": {
         "dataservices": {
           "redis": {
             "host_flavor": "bx3d.4x20"
           }
         },
         "service_endpoints": "private",
         "version": "8.2"
       },
       "tags": [
         "redis-test",
         "do-not-delete"
       ]
     }'
   ```
   {: pre}

   The fields in the command are described in the table that follows.

   | Field | Description | Flag |
   |-------|------------|------------|
   | `name` [Required]{: tag-red} | The instance name can be any string and is the name that is used on the web and in the CLI to identify the new deployment. |  |
   | `target` [Required]{: tag-red} | The location where you want to deploy. To retrieve a list of regions, use the `ibmcloud regions` command. |  |
   | `resource_group` [Required]{: tag-red} | The resource group ID. The default value is `Default`. | |
   | `resource_plan_id` [Required]{: tag-red} | Standard gen2 plan (`databases-for-redis`) |  |
   | `parameters` | JSON object of parameters to create service instance | |
   | `host_flavor` | For Isolated Compute, select your desired CPU and RAM configuration. For more information, see [Hosting models](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-isolated-compute).| |
   | `service_endpoints` [Required]{: tag-red} | Configure the [Service endpoints](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-private-endpoints-gen2){: external} of your deployment, either `public`, `private` or `public-and-private`. | |
   | `version` | The version of the database to be provisioned. If omitted, the database is created with the most recent major and minor version. | |
   | `tags` | Array of user tag names |  |
   {: caption="Table 3. Basic command format fields" caption-side="bottom"}

### Get details of the service instance
{: #get-service-instance-api}
{: api}

```sh
curl -X GET https://resource-controller.cloud.ibm.com/v2/resource_instances/<service-instance-id> -H "Authorization: ${IAM_TOKEN}" | jq
```
{: pre}

### Update a service instance
{: #update-service-instance-api}
{: api}

You can update the service instance name and parameters using the following command:

```sh
curl -X PATCH https://resource-controller.cloud.ibm.com/v2/resource_instances/<service-instance-id> -H "Authorization: ${IAM_TOKEN}" -H 'Content-Type: application/json' -d '{
"name": "redis-test-testing-patch",
"parameters":{"dataservices":{"redis":{"host_flavor":"bx3d.8x40"}}}
}' | jq
```
{: pre}

### Deleting a service instance
{: #delete-service-instance-api}
{: api}

Delete an instance by running the following command:

```sh
curl -X DELETE https://resource-controller.cloud.ibm.com/v2/resource_instances/<service-instance-id> -H "Authorization: ${IAM_TOKEN}"
```
{: pre}

### Restore from Redis backup
{: #restore-backup-api}
{: api}

You can provision a new Redis instance from a particular backup using the following command:

```sh
curl -X POST   https://resource-controller.cloud.ibm.com/v2/resource_instances   -H "Authorization: ${IAM_TOKEN}"   -H 'Content-Type: application/json' -d '{
    "name": "redis-test-restore",
    "target": "ca-mon",
    "resource_group": "c21a4e8564c14d1aab2a9a8b441904eb",
    "resource_plan_id": "databases-for-redis-gen2-standard",
    "parameters": {
      "dataservices": {
        "restore_backup_id": "<service-instance-crn>:backup:<backup-uuid>"
      }
    },
    "tags": [
      "redis-test"
    ]
  }'
```
{: pre}

## Provisioning with Terraform
{: #provisioning-terraform}
{: terraform}

Use Terraform to manage your infrastructure through the [`ibm_database` Resource for Terraform](https://registry.terraform.io/providers/IBM-Cloud/ibm/latest/docs/resources/database), which supports provisioning {{site.data.keyword.databases-for}} deployments.

Select the [hosting model](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-isolated-compute) you want your database to be provisioned on. You can change this later.

Provision a {{site.data.keyword.databases-for-redis}} instance with the `"host_flavor"` parameter set to the desired host flavor. For example:

```terraform
data "ibm_resource_group" "group" {
  name = "<your_resource_group>"
}

resource "ibm_resource_instance" "<your_instance_name>" {
  name = "your_instance_name"
  plan = "databases-for-redis-gen2-standard"
  service = "databases-for-redis"
  location = "us-east"
  tags              = ["terraform", "redis"]
  resource_group_id = data.ibm_resource_group.group.id

  timeouts {
    create = "120m"
    update = "120m"
    delete = "15m"
  }
}
```
{: codeblock}

Before executing a Terraform script on an existing instance, use the `terraform plan` command to compare the current infrastructure state with the desired state defined in your Terraform files. Any alteration to the `resource_group_id`, `service plan`, `version`, `key_protect_instance`, `key_protect_key`, or `backup_encryption_key_crn` attributes recreates your instance. For a list of current argument references with the `Forces new resource` specification, see the [ibm_database Terraform Registry](https://registry.terraform.io/providers/IBM-Cloud/ibm/latest/docs/resources/database){: external}.
{: important}

### Get details of the service instance
{: #get-service-instance-api}
{: terraform}

```terraform
data "ibm_resource_group" "group" {
  name = "<your_resource_group>"
}

data "ibm_resource_instance" "<your_instance_name>" {
  name              = "<your_instance_name>"
  location          = "us-east"
  resource_group_id = data.ibm_resource_group.group.id
  service = "databases-for-redis"
}


output "<your_instance_name>_output" {
  value ={
    id = data.ibm_resource_instance.<your_instance_name>.id
    name = data.ibm_resource_instance.<your_instance_name>.name
    extensions = data.ibm_resource_instance.<your_instance_name>.extensions
    group_id = data.ibm_resource_instance.<your_instance_name>.resource_group_id
  }
}
```
{: codeblock}

### Update a service instance
{: #update-service-instance-api}
{: terraform}

You can update the host flavor parameter using the following command:

```terraform
data "ibm_resource_group" "group" {
  name = "your_resource_group"
}

resource "ibm_resource_instance" "<your_instance_name>" {
  name = "<your_instance_name>"
  plan = "databases-for-redis-gen2-standard"
  service = "databases-for-redis"
  location = "us-east"
  tags              = ["scaling","tf"]
  resource_group_id = data.ibm_resource_group.group.id

  parameters_json = jsonencode({
  "dataservices": {
      "redis": {
        "host_flavor": "bx2.8x32"
      }
  }
})
  timeouts {
    create = "120m"
    update = "120m"
    delete = "15m"
  }
}
```
{: codeblock}

### Deleting a service instance
{: #delete-service-instance-terraform}
{: terraform}

To delete resources managed by Terraform, run: `terraform destroy`

- When Terraform creates a user, it automatically creates a **state file (terraform.tfstate)** and the details get added in the resources of the state file. This file tracks all managed resources and is used by Terraform to identify what to destroy during deletion.

- Terraform can only delete resources that it created. Resources provisioned through the UI, CLI, or API cannot be destroyed by Terraform **unless they have first been imported into the Terraform state**.

- All entries present in the resources of the state file are deleted when you run destroy. Hence, make sure the resources that you want to delete are the only ones in the resources list before running `terraform destroy`

### Restore from Redis backup
{: #restore-backup-api}
{: terraform}

You can provision a new Redis instance from a particular backup using the following command:

```terraform
resource "ibm_resource_instance" "<your_instance_name>" {
  name = "<your_instance_name>"
  plan = "databases-for-redis-gen2-standard"
  service = "databases-for-redis"
  location = "us-east"
  tags      = ["terraform", "redis"]

    parameters_json = jsonencode({
      "dataservices": {
        "restore_backup_id": "<service-instance-crn>:backup:<backup-uuid>"
      }
    })
}
```
{: codeblock}
