---

copyright:
  years: 2026
lastupdated: "2026-06-15"

keywords: redis gui, redis, redis cloud database, redis getting started, Gen 2, sentinel

subcollection: databases-for-redis-gen2

content-type: tutorial
services:
account-plan: paid
completion-time: 30m

---

{{site.data.keyword.attribute-definition-list}}

# Getting started with {{site.data.keyword.databases-for-redis_full}}
{: #getting-started}
{: toc-content-type="tutorial"}
{: toc-services=""}
{: toc-completion-time="30m"}

This tutorial guides you through the steps to quickly start using {{site.data.keyword.databases-for-redis}} on the Gen 2 platform by provisioning an instance, setting up a secure connection through a VSI and VPE, and enabling logging and monitoring.

Follow these steps to complete the tutorial: {: ui}

* [Before you begin](#prereqs)
* [Step 1: Provision using the console](#provision_instance_ui)
* [Step 2: Creating the `Manager` user using the console](#manager_user_ui)
* [Step 3: Create a connection](#private_connect_setup_ui)
* [Step 4: Connect {{site.data.keyword.mon_full_notm}}](#connect_monitoring_ui)
* [Step 5: Connect {{site.data.keyword.atracker_full}}](#activity_tracker_ui)
* [Next Steps](#next_steps)
{: ui}

Follow these steps to complete the tutorial: {: cli}

* [Before you begin](#prereqs)
* [Step 1: Provision using the CLI](#provision_instance_cli)
* [Step 2: Creating the `Manager` user using the CLI](#manager_user_cli)
* [Step 3: Create a connection](#private_connect_setup_cli)
* [Step 4: Connect {{site.data.keyword.mon_full_notm}}](#connect_monitoring_cli)
* [Step 5: Connect {{site.data.keyword.atracker_full}}](#activity_tracker_cli)
* [Next Steps](#next_steps)
{: cli}

Follow these steps to complete the tutorial: {: api}

* [Before you begin](#prereqs)
* [Step 1: Provision using the API](#provision_instance_api)
* [Step 2: Creating the `Manager` user using the API](#manager_user_api)
* [Step 3: Create a connection](#private_connect_setup_api)
* [Step 4: Connect {{site.data.keyword.mon_full_notm}}](#connect_monitoring_api)
* [Step 5: Connect {{site.data.keyword.atracker_full}}](#activity_tracker_api)
* [Next Steps](#next_steps)
{: api}

Follow these steps to complete the tutorial: {: terraform}

* [Before you begin](#prereqs)
* [Step 1: Provision using Terraform](#provision_instance_tf)
* [Step 2: Creating the `Manager` user using Terraform](#manager_user_tf)
* [Step 3: Create a connection](#private_connect_setup_tf)
* [Step 4: Connect {{site.data.keyword.mon_full_notm}}](#connect_monitoring_tf)
* [Step 5: Connect {{site.data.keyword.atracker_full}}](#activity_tracker_tf)
* [Next Steps](#next_steps)
{: terraform}


## Before you begin
{: #prereqs}

* You need an [{{site.data.keyword.cloud_notm}} account](https://cloud.ibm.com/registration){: external}.


## Step 1: Provision using the console
{: #provision_instance_ui}
{: ui}

1. Log in to the {{site.data.keyword.cloud_notm}} console.
1. Click the [**{{site.data.keyword.databases-for-redis}} service**](https://cloud.ibm.com/catalog){: external} in the **catalog**.

1. In **Service details**, configure the following:
    - **Location** Select a location that supports Gen 2.
    - **Service name** The name can be any string and is the name that is used on the web and in the CLI to identify the new instance.
    - **Resource group** Required if you are organizing your services into resource groups. For more information, see [Managing resource groups](/docs/account?topic=account-rgs).

1. **Resource allocation** Select an isolated compute instance with a defined amount of RAM and CPU cores. Changing resource allocation requires selecting a different host size. *After provisioning, disk cannot be scaled down.*
1. In **Service configuration**, configure the following:
    - **Database version** [Set only at deployment]{: tag-red} The deployment version of your database. To ensure optimal performance, run the preferred version. The latest minor version is used automatically. For more information, see [Database versioning policy](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-versioning-policy&interface=ui){: external}.
    - **Encryption** If you use [Key Protect](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-key-protect&interface=ui), an instance and key can be selected to encrypt the instance's disk. If you do not use your own key, the instance automatically creates and manages its own disk encryption key.

1. Click **Create**. The {{site.data.keyword.databases-for}} **Resource list** page opens.

1. When your instance has been provisioned, click the instance name to view more information.

As part of provisioning a new instance in {{site.data.keyword.cloud}}, you can use the service credential console page to create a user with different roles (Manager and Writer).
{: note}

{{site.data.keyword.databases-for-redis}} instances no longer include a default `admin` user. Instead, customers create a user with the `Manager` or `Writer` role using the {{site.data.keyword.cloud}} service credential interface using the UI or CLI. These users come with necessary credentials to connect to and manage the instance.


## Step 1: Provision using the CLI
{: #provision_instance_cli}
{: cli}

You can provision a {{site.data.keyword.databases-for-redis}} instance using the CLI. If you don't already have it, you need to install the [{{site.data.keyword.cloud_notm}} CLI](https://www.ibm.com/cloud/cli){: external}.

1. Log in to {{site.data.keyword.cloud_notm}} with the following command:
{: #step2_login_qsg}

    ```sh
    ibmcloud login
    ```
    {: pre}

    If you use a federated user ID, it's important that you switch to a one-time passcode (`ibmcloud login --sso`), or use an API key (`ibmcloud --apikey key` or `@key_file`) to authenticate. For more information about how to log in using the CLI, see [General CLI (ibmcloud) commands](/docs/cli?topic=cli-ibmcloud_cli#ibmcloud_login) under `ibmcloud login`.

1. Create a {{site.data.keyword.databases-for-redis}} instance.
{: #step3_es_instance}

    Select one of the following methods:

    * To create an instance from the CLI on the Enterprise plan, run the following command:

        ```sh
        ibmcloud resource service-instance-create <INSTANCE_NAME> databases-for-redis standard-gen2 <LOCATION> -g <RESOURCE_GROUP>
        ```
        {: codeblock}

      This provisions a Redis instance with the default of 2 members and 10 GB disk, as well as the smallest host flavor available in the location you selected.

      Alternatively, you can specify custom values for these parameters by passing in the parameters flag:

      ```sh
      ibmcloud resource service-instance-create <INSTANCE_NAME> databases-for-redis standard-gen2 <LOCATION> -g <RESOURCE_GROUP> -p '{"dataservices": {"redis": {"storage_gb": 40, "members": 3, "host_flavor": "bx3d.8x40"}}}'
      ```
      {: codeblock}

      This will provision a Redis instance with 3 members and 40 GB of storage per member running on hosts of flavor bx3d.8x40.

      If you pass in unsupported values, the **create** command fails with a message indicating which values are invalid.

      Supported parameters:

      - storage_gb (valid integer values between 10 and 9600, representing disk storage per member in GB)
      - members (valid integer values '2' and '3', indicating whether to run Redis with 2-zone HA or 3-zone HA)
      - host_flavor (values depend on location)

   The fields in the command are described in the following table:

   | Field | Description | Flag |
   |-------|------------|------------|
   | `NAME` [Required]{: tag-red} | The instance name can be any string and is the name that is used on the web and in the CLI to identify the new instance. |  |
   | `SERVICE_NAME` [Required]{: tag-red} | Name or ID of the service. For {{site.data.keyword.databases-for-redis}}, use `databases-for-redis`. |  |
   | `SERVICE_PLAN_NAME` [Required]{: tag-red} | Standard plan (`standard`). |  |
   | `LOCATION` [Required]{: tag-red} | The location where you want to deploy. To retrieve a list of regions, use the `ibmcloud regions` command. |  |
   | `SERVICE_ENDPOINTS_TYPE` | Configure the [Service endpoints](/docs/cloud-databases?topic=cloud-databases-service-endpoints) of your instance, `private`. |  |
   | `RESOURCE_GROUP` | The Resource group name. The default value is `default`. | -g |
   | `--parameters` | JSON file or JSON string of parameters to create service instance. | -p |
   {: caption="Basic command format fields" caption-side="top"}

1. Use the following command to check the provisioning status:

   ```sh
   ibmcloud resource service-instance <INSTANCE_NAME>
   ```
   {: pre}


### Connect to your database with the CLI
{: #connecting-cli}
{: cli}

Find the appropriate commands to connect to your database from the CLI in [{{site.data.keyword.databases-for}} CLI Reference](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-cdb-reference) and [Connecting with redis-cli](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-connecting-cli-client).

The `ibmcloud cdb deployment-connections` command handles everything that is involved in creating a CLI connection. For example, to connect to an instance named "example-redis", use a command like:

```sh
ibmcloud cdb deployment-connections example-redis --start
```
{: pre}

The command prompts for the `Manager` user password and then runs the `redis-cli` CLI to connect to the database.

### The `--parameters` parameter
{: #flags-params-service-endpoints}
{: cli}

The `service-instance-create` command supports a `-p` flag, which allows JSON-formatted parameters to be passed to the provisioning process. Some parameter values are Cloud Resource Names (CRNs), which uniquely identify a resource in the cloud. All parameter names and values are passed as strings.

For example, if a database is being provisioned from a particular backup and the new database instance needs a total of 9 GB of memory across three members, the command to provision 3 GB per member looks like:

```sh
ibmcloud resource service-instance-create databases-for-redis <SERVICE_NAME> standard us-south \
-p \ '{
  "backup_id": "crn:v1:blue:public:databases-for-redis:us-south:a/54e8ffe85dcedf470db5b5ee6ac4a8d8:1b8f53db-fc2d-4e24-8470-f82b15c71717:backup:06392e97-df90-46d8-98e8-cb67e9e0a8e6",
  "members_memory_allocation_mb": "3072"
}'
```
{: .pre}


## Step 1: Provision using the resource controller API
{: #provision_instance_api}
{: api}

Complete these steps to provision by using the [resource controller API](https://cloud.ibm.com/apidocs/resource-controller/resource-controller){: external}.

1. Obtain an [IAM token from your API token](https://cloud.ibm.com/apidocs/resource-controller/resource-controller#authentication){: external}.
1. You need to know the ID of the resource group you want to deploy to. This information is available using the [{{site.data.keyword.cloud_notm}} CLI](/docs/cli?topic=cli-ibmcloud_commands_resource#ibmcloud_resource_groups).

   Use a command like:

   ```sh
   ibmcloud resource groups
   ```
   {: pre}

1. You need to know the region where you want to deploy.

   To list all of the regions that instances can be provisioned into from the current region, use the [{{site.data.keyword.databases-for}} CLI](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-cdb-reference){: external}.

   The command looks like:

   ```sh
   ibmcloud cdb regions --json
   ```
   {: pre}

   When you have all the information, [provision a new resource instance](https://cloud.ibm.com/apidocs/resource-controller/resource-controller#create-resource-instance){: external} with the {{site.data.keyword.cloud_notm}} resource controller.

   ```sh
   curl -X POST \
     https://resource-controller.cloud.ibm.com/v2/resource_instances \
     -H 'Authorization: Bearer <>' \
     -H 'Content-Type: application/json' \
       -d '{
       "name": "my-instance",
       "target": "blue-us-south",
       "resource_group": "5g9f447903254bb58972a2f3f5a4c711",
       "resource_plan_id": "databases-for-redis-standard"
       "parameters": {
            "storage_gb": 10,
            "members": 2,
            "host_flavor": "bx2.4x16",
            "service_endpoints": "private"
      }
     }'
   ```
   {: .pre}

   The parameters `name`, `target`, `resource_group`, and `resource_plan_id` are all required.
   {: required}

Supported parameters:

      - `storage_gb` (valid integer values are between 10 and 9600, representing disk storage per member in GB)
      - `members`(valid integer values are '2' and '3', indicating whether to run Redis with two-zone HA or three-zone HA)
      - `host_flavor`(values depend on location).

## List of additional parameters
{: #provisioning-parameters-api}
{: api}

* `backup_id` A CRN of a backup resource to restore from. The backup must be created by a database instance with the same service ID. The backup is loaded after provisioning and the new instance starts up that uses that data. A backup CRN is in the format `crn:v1:<...>:backup:<uuid>`. If omitted, the database is provisioned empty.
* `version` The version of the database to be provisioned. If omitted, the database is created with the most recent major and minor version.
* `disk_encryption_key_crn` The CRN of a KMS key ([{{site.data.keyword.keymanagementserviceshort}}](/docs/key-protect?topic=key-protect-about)), which is then used for disk encryption. A KMS key CRN is in the format `crn:v1:<...>:key:<id>`.
* `backup_encryption_key_crn` The CRN of a KMS key (for example, [{{site.data.keyword.keymanagementserviceshort}}](/docs/key-protect?topic=key-protect-about)), which is then used for backup encryption. A KMS key CRN is in the format `crn:v1:<...>:key:<id>`.

   To use a key for your backups, you must first [enable the service-to-service delegation](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-key-protect&interface=ui#key-byok).
   {: note}

* `service_endpoints` The [Service endpoints](/docs/cloud-databases?topic=cloud-databases-service-endpoints) supported on your instance,`private`. This is a required parameter.


## Step 1: Provision using Terraform
{: #provision_instance_tf}
{: terraform}

Initialize your Terraform project:

```sh
terraform init
```
{: pre}

This downloads the {{site.data.keyword.cloud_notm}} provider plug-in and initializes your workspace.

Review the planned changes:

```sh
terraform plan
```
{: pre}

If the plan looks correct, apply the configuration:

```sh
terraform apply
```
{: pre}

Type `yes` when prompted to confirm. The provisioning process takes approximately 15-20 minutes.


## Step 2: Creating the `Manager` user using  console
{: #manager_user_ui}
{: ui}

### The `Manager` user

As part of provisioning a new instance in {{site.data.keyword.cloud}}, you can use the service credential console page to create a user with different roles (Manager and Writer).

{{site.data.keyword.databases-for-redis}} instances no longer include a default admin user. Instead, you create a user with the `Manager` or `Writer` role using the {{site.data.keyword.cloud}} service credential interface using the UI or CLI. These users come with necessary credentials to connect to and manage the instance.

The `Manager` user functions as an admin-like user with full access to Redis commands and operations. The created user has comprehensive permissions for managing the Redis instance.

### Change the user password in the console
{: #user-management-set-manager-password-ui}
{: ui}

Changing the user password is not supported using the {{site.data.keyword.cloud_notm}} console on Gen 2.


## Step 2: Creating the `Manager` user using the CLI
{: #manager_user_cli}
{: cli}

{{site.data.keyword.databases-for-redis}} instances no longer include a default admin user. Instead, you create a user with the `Manager` or `Writer` role using the {{site.data.keyword.cloud}} service credential interface using the UI or CLI. These users come with necessary credentials to connect to and manage the instance.

Use one of the following commands from the {{site.data.keyword.cloud_notm}} CLI {{site.data.keyword.databases-for}} plug-in to create the `Manager` user.

```sh
ibmcloud resource service-key-create <service_key_name> Manager --instance-name <instance_name>
```
{: pre}

```sh
ibmcloud resource service-key-create <service_key_name> Manager --instance-id <guid>
```
{: pre}

Similarly, for creating a user with the `Writer` role, use the following command:

```
ibmcloud resource service-key-create <service_key_name> --instance-name <INSTANCE_NAME> -p '{"role_crn": "crn:v1:bluemix:public:iam::::serviceRole:Writer"}'
```
{: pre}

### Delete the user in the CLI
{: #manager_user_del_cli}
{: cli}

Use the following command from the {{site.data.keyword.cloud_notm}} CLI {{site.data.keyword.databases-for}} plug-in to delete the created user.

```sh
ibmcloud resource service-key-delete <service_key_name>
```
{: pre}

### Change the manager password in the CLI
{: #manager_pw_set_cli}
{: cli}

Changing a user password is not supported using the CLI on Gen 2. However, you can update a password using tools, such as `redis-cli` by running the appropriate Redis commands.


## Step 2: Creating the `Manager` user using  API
{: #manager_user_api}
{: api}

As part of provisioning a new instance in {{site.data.keyword.cloud}}, you can use the service credential console page to create a user with different roles (Manager and Writer).

{{site.data.keyword.databases-for-redis}} instances no longer include a default admin user. Instead, you create a user with the `Manager` or `Writer` role using the {{site.data.keyword.cloud}} service credential interface using the UI or CLI. These users come with necessary credentials to connect to and manage the instance.


## Step 2: Creating the `Manager` user using Terraform
{: #manager_user_tf}
{: terraform}

As part of provisioning a new instance in {{site.data.keyword.cloud}}, you can use the service credential console page to create a user with different roles (Manager and Writer).

{{site.data.keyword.databases-for-redis}} instances no longer include a default admin user. Instead, you create a user with the `Manager` or `Writer` role using the {{site.data.keyword.cloud}} service credential interface using the UI or CLI. These users come with necessary credentials to connect to and manage the instance.


## Step 3: Create a connection
{: #private_connect_setup_ui}
{: ui}

The **Connect** tab in Gen 2 provides guided instructions for creating a secure connection to your {{site.data.keyword.databases-for-redis}} deployment.

Because Gen 2 supports **private endpoints only**, all connections are established through the {{site.data.keyword.cloud}} private network. The _Create a connection_ view walks you through the required setup to connect securely from your infrastructure, such as a Virtual Server Instance (VSI), by using Virtual Private Endpoint (VPE) gateway.

This guided experience is designed to help you configure a production-ready, secure connection without exposing your database to the public internet.

The following links provide a clear overview of how a connection is established within the VPC environment.

* [Create a VPC](https://cloud.ibm.com/infrastructure/network/vpcs/) (Virtual Private Cloud): A VPC is your own isolated network within {{site.data.keyword.cloud}} where you can securely run resources.
* [Generate an SSH key](https://cloud.ibm.com/infrastructure/compute/sshKeys/): SSH keys allow you to securely connect to your virtual servers.
* [Provision a Virtual Server Instance (VSI)](https://cloud.ibm.com/infrastructure/compute/vs/): A VSI is your cloud-based server where applications and workloads run.
* [Reserve a floating IP for your VSI](https://cloud.ibm.com/infrastructure/network/floatingIPs/): A floating IP is a public IP address that lets you access your VSI from the internet.
* [Create a Virtual Private Endpoint (VPE)](https://cloud.ibm.com/infrastructure/network/endpointGateways/): A VPE provides secure, private connectivity to {{site.data.keyword.cloud_notm}} services.

## Step 3: Create a connection
{: #private_connect_setup_cli}
{: cli}

The **Connect** tab in Gen 2 provides guided instructions for creating a secure connection to your {{site.data.keyword.databases-for-redis}} deployment.

Because Gen 2 supports **private endpoints only**, all connections are established through the {{site.data.keyword.cloud}} private network. The _Create a connection_ view walks you through the required setup to connect securely from your infrastructure, such as a Virtual Server Instance (VSI), by using Virtual Private Endpoint (VPE) gateway.

This guided experience is designed to help you configure a production-ready, secure connection without exposing your database to the public internet.

Also, the following linked sections provide a clear overview of how a connection is established within the VPC environment.

* [Create a VPC](https://cloud.ibm.com/infrastructure/network/vpcs/) (Virtual Private Cloud): A VPC is your own isolated network within {{site.data.keyword.cloud}} where you can securely run resources.
* [Generate an SSH key](https://cloud.ibm.com/infrastructure/compute/sshKeys/): SSH keys allow you to securely connect to your virtual servers.
* [Provision a Virtual Server Instance (VSI)](https://cloud.ibm.com/infrastructure/compute/vs/): A VSI is your cloud-based server where applications and workloads run.
* [Reserve a floating IP for your VSI](https://cloud.ibm.com/infrastructure/network/floatingIPs/): A floating IP is a public IP address that lets you access your VSI from the internet.
* [Create a Virtual Private Endpoint (VPE)](https://cloud.ibm.com/infrastructure/network/endpointGateways/): A VPE provides secure, private connectivity to {{site.data.keyword.cloud_notm}} services.


## Step 3: Create a connection
{: #private_connect_setup_api}
{: api}

The **Connect** tab in Gen 2 provides guided instructions for creating a secure connection to your {{site.data.keyword.databases-for-redis}} deployment.

Because Gen 2 supports **private endpoints only**, all connections are established through the {{site.data.keyword.cloud}} private network. The _Create a connection_ view walks you through the required setup to connect securely from your infrastructure, such as a Virtual Server Instance (VSI), by using Virtual Private Endpoint (VPE) gateway.

This guided experience is designed to help you configure a production-ready, secure connection without exposing your database to the public internet.

Also, the following linked sections provide a clear overview of how a connection is established within the VPC environment.

* [Create a VPC](https://cloud.ibm.com/infrastructure/network/vpcs/) (Virtual Private Cloud): A VPC is your own isolated network within {{site.data.keyword.cloud}} where you can securely run resources.
* [Generate an SSH key](https://cloud.ibm.com/infrastructure/compute/sshKeys/): SSH keys allow you to securely connect to your virtual servers.
* [Provision a Virtual Server Instance (VSI)](https://cloud.ibm.com/infrastructure/compute/vs/): A VSI is your cloud-based server where applications and workloads run.
* [Reserve a floating IP for your VSI](https://cloud.ibm.com/infrastructure/network/floatingIPs/): A floating IP is a public IP address that lets you access your VSI from the internet.
* [Create a Virtual Private Endpoint (VPE)](https://cloud.ibm.com/infrastructure/network/endpointGateways/): A VPE provides secure, private connectivity to {{site.data.keyword.cloud_notm}} services.


## Step 3: Create a connection
{: #private_connect_setup_tf}
{: terraform}

The **Connect** tab in Gen 2 provides guided instructions for creating a secure connection to your {{site.data.keyword.databases-for-redis}} deployment.

Because Gen 2 supports **private endpoints only**, all connections are established through the {{site.data.keyword.cloud}} private network. The _Create a connection_ view walks you through the required setup to connect securely from your infrastructure, such as a Virtual Server Instance (VSI), by using Virtual Private Endpoint (VPE) gateway.

This guided experience is designed to help you configure a production-ready, secure connection without exposing your database to the public internet.

Also, the following linked sections provide a clear overview of how a connection is established within the VPC environment.

* [Create a VPC](https://cloud.ibm.com/infrastructure/network/vpcs/) (Virtual Private Cloud): A VPC is your own isolated network within {{site.data.keyword.cloud}} where you can securely run resources.
* [Generate an SSH key](https://cloud.ibm.com/infrastructure/compute/sshKeys/): SSH keys allow you to securely connect to your virtual servers.
* [Provision a Virtual Server Instance (VSI)](https://cloud.ibm.com/infrastructure/compute/vs/): A VSI is your cloud-based server where applications and workloads will run.
* [Reserve a floating IP for your VSI](https://cloud.ibm.com/infrastructure/network/floatingIPs/): A floating IP is a public IP address that lets you access your VSI from the internet.
* [Create a Virtual Private Endpoint (VPE)](https://cloud.ibm.com/infrastructure/network/endpointGateways/): A VPE provides secure, private connectivity to {{site.data.keyword.cloud_notm}} services.


### Connect with redis-cli
{: #tf_connect_redis}
{: terraform}

Ensure that you have Redis client tools installed. Check your `redis-cli` version:

```sh
redis-cli --version
```
{: pre}

Connect to your database using the connection string from your Terraform outputs.


## Step 4: Connect {{site.data.keyword.mon_full_notm}} using the console
{: #connect_monitoring_ui}
{: ui}

You can use {{site.data.keyword.mon_full_notm}} to get operational visibility into the performance and health of your applications, services, and platforms. {{site.data.keyword.mon_full_notm}} provides administrators, DevOps teams, and developers full stack telemetry with advanced features to monitor and troubleshoot, define alerts, and design custom dashboards.

For more information about how to use {{site.data.keyword.monitoringshort}} with {{site.data.keyword.databases-for-redis}}, see [Monitoring integration](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-monitoring&interface=ui).

You cannot connect {{site.data.keyword.mon_full_notm}} by using the CLI. Use the console to complete this task. For more information, see [Monitoring integration](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-monitoring&interface=ui).
{: note}


## Step 4: Connect {{site.data.keyword.mon_full_notm}} using the CLI
{: #connect_monitoring_cli}
{: cli}

You can use {{site.data.keyword.mon_full_notm}} to get operational visibility into the performance and health of your applications, services, and platforms. {{site.data.keyword.mon_full_notm}} provides administrators, DevOps teams, and developers full stack telemetry with advanced features to monitor and troubleshoot, define alerts, and design custom dashboards.

For more information about how to use {{site.data.keyword.monitoringshort}} with {{site.data.keyword.databases-for-redis}}, see [Monitoring integration](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-monitoring&interface=ui).

You cannot connect {{site.data.keyword.mon_full_notm}} by using the CLI. Use the console to complete this task. For more information, see [Monitoring integration](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-monitoring&interface=ui).
{: note}


## Step 4: Connect {{site.data.keyword.mon_full_notm}} using the API
{: #connect_monitoring_api}
{: api}

You can use {{site.data.keyword.mon_full_notm}} to get operational visibility into the performance and health of your applications, services, and platforms. {{site.data.keyword.mon_full_notm}} provides administrators, DevOps teams, and developers full stack telemetry with advanced features to monitor and troubleshoot, define alerts, and design custom dashboards.

For more information about how to use {{site.data.keyword.monitoringshort}} with {{site.data.keyword.databases-for-redis}}, see [Monitoring integration](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-monitoring&interface=ui).

You cannot connect {{site.data.keyword.mon_full_notm}} by using the CLI. Use the console to complete this task. For more information, see [Monitoring integration](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-monitoring&interface=ui).
{: note}


## Step 4: Connect {{site.data.keyword.mon_full_notm}} using Terraform
{: #connect_monitoring_tf}
{: terraform}

You can use {{site.data.keyword.mon_full_notm}} to get operational visibility into the performance and health of your applications, services, and platforms. {{site.data.keyword.mon_full_notm}} provides administrators, DevOps teams, and developers full stack telemetry with advanced features to monitor and troubleshoot, define alerts, and design custom dashboards.

For more information about how to use {{site.data.keyword.monitoringshort}} with {{site.data.keyword.databases-for-redis}}, see [Monitoring integration](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-monitoring&interface=ui).

You cannot connect {{site.data.keyword.mon_full_notm}} by using the CLI. Use the console to complete this task. For more information, see [Monitoring integration](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-monitoring&interface=ui).
{: note}


## Step 5: Connect {{site.data.keyword.atracker_full_notm}}
{: #activity_tracker_ui}
{: ui}

{{site.data.keyword.atracker_full}} allows you to view, and audit service activity to comply with corporate policies and industry regulations. {{site.data.keyword.atracker_short}} records user-initiated activities that change the state of a service in {{site.data.keyword.cloud_notm}}. Use {{site.data.keyword.atracker_short}} to track how users and applications interact with the {{site.data.keyword.databases-for-redis}} service.

To get up and running with {{site.data.keyword.atracker_full_notm}}, see [Getting started with {{site.data.keyword.atracker_full_notm}}](/docs/atracker?topic=atracker-getting-started){: external}.

{{site.data.keyword.atracker_short}} can have only one instance per location. To view events, you must access the web UI of the {{site.data.keyword.atracker_short}} service in the same location where your service instance is available. For more information, see [Launch the web UI](/docs/cloud-logs?topic=cloud-logs-getting-started){: external}.

For more information about events specific to {{site.data.keyword.databases-for-redis}}, see [{{site.data.keyword.atracker_short}} events](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-at_events&interface=api).

Events are formatted according to the Cloud Auditing Data Federation (CADF) standard. For further details of the information they include, see [CADF standard](/docs/atracker?topic=atracker-event){: external}.

You cannot connect {{site.data.keyword.atracker_short}} by using the CLI. Use the console to complete this task. For more information, see [Activity tracking events](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-at_events&interface=api).
{: note}


## Step 5: Connect {{site.data.keyword.atracker_full_notm}} using the CLI
{: #activity_tracker_cli}
{: cli}

{{site.data.keyword.atracker_full}} allows you to view and audit service activity to comply with corporate policies and industry regulations. {{site.data.keyword.atracker_short}} records user-initiated activities that change the state of a service in {{site.data.keyword.cloud_notm}}. Use {{site.data.keyword.atracker_short}} to track how users and applications interact with the {{site.data.keyword.databases-for-redis}} service.

To get up and running with {{site.data.keyword.atracker_full_notm}}, see [Getting started with {{site.data.keyword.atracker_full_notm}}](/docs/atracker?topic=atracker-getting-started){: external}.

{{site.data.keyword.atracker_short}} can have only one instance per location. To view events, you must access the web UI of the {{site.data.keyword.atracker_short}} service in the same location where your service instance is available. For more information, see [Launch the web UI](/docs/cloud-logs?topic=cloud-logs-getting-started){: external}.

For more information about events specific to {{site.data.keyword.databases-for-redis}}, see [{{site.data.keyword.atracker_short}} events](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-at_events&interface=api).

Events are formatted according to the Cloud Auditing Data Federation (CADF) standard. For further details of the information they include, see [CADF standard](/docs/atracker?topic=atracker-event){: external}.

You cannot connect {{site.data.keyword.atracker_short}} by using the CLI. Use the console to complete this task. For more information, see [Activity tracking events](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-at_events&interface=api).
{: note}


## Step 5: Connect {{site.data.keyword.atracker_full}} using the API
{: #activity_tracker_api}
{: api}

{{site.data.keyword.atracker_full}} allows you to view and audit service activity to comply with corporate policies and industry regulations. {{site.data.keyword.atracker_short}} records user-initiated activities that change the state of a service in {{site.data.keyword.cloud_notm}}. Use {{site.data.keyword.atracker_short}} to track how users and applications interact with the {{site.data.keyword.databases-for-redis}} service.

To get up and running with {{site.data.keyword.atracker_full_notm}}, see [Getting started with {{site.data.keyword.atracker_full_notm}}](/docs/atracker?topic=atracker-getting-started){: external}.

{{site.data.keyword.atracker_short}} can have only one instance per location. To view events, you must access the web UI of the {{site.data.keyword.atracker_short}} service in the same location where your service instance is available. For more information, see [Launch the web UI](/docs/cloud-logs?topic=cloud-logs-getting-started){: external}.

For more information about events specific to {{site.data.keyword.databases-for-redis}}, see [{{site.data.keyword.atracker_short}} events](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-at_events&interface=api).

Events are formatted according to the Cloud Auditing Data Federation (CADF) standard. For further details of the information they include, see [CADF standard](/docs/atracker?topic=atracker-event){: external}.

You cannot connect {{site.data.keyword.atracker_short}} by using the CLI. Use the console to complete this task. For more information, see [Activity tracking events](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-at_events&interface=api).
{: note}


## Step 5: Connect {{site.data.keyword.atracker_full_notm}} using Terraform
{: #activity_tracker_tf}
{: terraform}

{{site.data.keyword.atracker_full}} allows you to view and audit service activity to comply with corporate policies and industry regulations. {{site.data.keyword.atracker_short}} records user-initiated activities that change the state of a service in {{site.data.keyword.cloud_notm}}. Use {{site.data.keyword.atracker_short}} to track how users and applications interact with the {{site.data.keyword.databases-for-redis}} service.

To get up and running with {{site.data.keyword.atracker_full_notm}}, see [Getting started with {{site.data.keyword.atracker_full_notm}}](/docs/atracker?topic=atracker-getting-started){: external}.

{{site.data.keyword.atracker_short}} can have only one instance per location. To view events, you must access the web UI of the {{site.data.keyword.atracker_short}} service in the same location where your service instance is available. For more information, see [Launch the web UI](/docs/cloud-logs?topic=cloud-logs-getting-started){: external}.

For more information about events specific to {{site.data.keyword.databases-for-redis}}, see [{{site.data.keyword.atracker_short}} events](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-at_events&interface=api).

Events are formatted according to the Cloud Auditing Data Federation (CADF) standard. For further details of the information they include, see [CADF standard](/docs/atracker?topic=atracker-event){: external}.

You cannot connect {{site.data.keyword.atracker_short}} by using the CLI. Use the console to complete this task. For more information, see [Activity tracking events](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-at_events&interface=api).
{: note}


## Next steps
{: #next-steps}

- If you are using {{site.data.keyword.databases-for-redis}} for the first time, see the [official {{site.data.keyword.databases-for-redis}} documentation](https://redis.io/documentation){: external}.
- Connect your instance to [IBM Cloud Log Analysis](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-logging&interface=ui) and [IBM Cloud Monitoring](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-monitoring&interface=ui) for observability and alerting.
- Connect to and manage your databases and data with {{site.data.keyword.databases-for-redis}}'s CLI tool [`redis-cli`](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-connecting-cli-client).
- Looking for more tools on managing your databases? Connect to your instance with the following tools:

    - [{{site.data.keyword.cloud_notm}} CLI](/docs/cli?topic=cli-install-ibmcloud-cli){: external}
    - [{{site.data.keyword.databases-for}} CLI](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-cdb-reference){: external}
    - [{{site.data.keyword.databases-for}} API](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-api){: external}

- If you plan to use {{site.data.keyword.databases-for-redis}} for your applications, see:

    - [Connecting an external application](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-external-app)
    - [Connecting an {{site.data.keyword.cloud_notm}} application](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-ibmcloud-app)

- To ensure the stability of your applications and your databases, see:

    - [High availability](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-redis-ha-dr)
    - [Performance](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-performance&interface=ui)
