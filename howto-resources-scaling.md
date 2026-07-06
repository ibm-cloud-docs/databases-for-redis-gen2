---

copyright:
  years: 2026
lastupdated: "2026-06-25"

keywords: redis, databases, scaling, manual scaling, disk I/O, memory, CPU

subcollection: databases-for-redis-gen2

---

{{site.data.keyword.attribute-definition-list}}

# Adding disk, memory, and CPU
{: #resources-scaling}

[Gen 2]{: tag-purple}

You can manually adjust the amount of resources available to your {{site.data.keyword.databases-for-redis_full}} deployment to suit your workload and the size of your data.

## Resource breakdown
{: #resources-scaling-breakdown}

{{site.data.keyword.databases-for-redis}} deployments consist of 2 member nodes and 3 sentinel nodes for high availability. Resources are allocated to the 2 member nodes, which are the billable components:

**Storage:**
- Minimum storage: 10 GB per member (20 GB total for the deployment)
- Maximum storage: 4000 GB per member (8000 GB total for the deployment)
- Storage is allocated equally to both member nodes
- Storage can be scaled up dynamically

**Compute resources:**
- Resources are determined by the selected host flavor (for example, bx2.4x16, bx3d.4x20, or bxf.4x16).
- Host flavors define CPU and memory allocation (for example, bx2.4x16 = 4 vCPU, 16 GB RAM per member).
- Both member nodes use the same host flavor.
- The 3rd sentinel-only node runs on the smallest worker node and is not billed separately.

**Deployment topology:**
- 2 member pods: run Redis data + colocated sentinel
- 3 sentinel pods total: 2 colocated with members + 1 standalone sentinel
- Pods are distributed across 3 availability zones for high availability
- Only the 2 member nodes are included in billing calculations

Billing is based on the total amount of resources that are allocated to the service.
{: note}

When you [provision](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-provisioning) a deployment, you can select the initial resource allocation of disk and memory. After provision, you can scale your deployment as it needs more resources.

### Disk usage
{: #resources-scaling-disk}

By default, {{site.data.keyword.databases-for-redis}} uses disk for data persistence. Your disk allocation per data member has to be enough to store your data. When you add disk to the total allocation, it adds it to both members equally.

Performance scales with disk size, meaning larger disk allocation can provide higher throughput and IOPS. Baseline Input-Output Operations per second (IOPS) performance for disk is 10 IOPS for each GB. Scale disk to increase the IOPS your deployment can handle.

If you have configured Redis as a cache, persistence has been disabled on your deployment. If you re-enable Redis persistence, be sure to scale your disk first to prevent losing data.

You cannot scale down storage. You can recover space by backing up and restoring to a new deployment.
{: tip}

### Memory
{: #resources-scaling-memory}

**Memory allocation and scaling:** By default, your deployment is configured with a `noeviction` policy, so your memory resources should be scaled to fit your data set. Each of the 2 data members contains a complete copy of your data, meaning the total memory usage across your deployment is approximately twice the size of your data set. When you scale memory, the allocation is applied equally to both members.

**Host flavor selection:** Select the host flavor that matches your resource needs:
- **Isolated Compute profiles (bx3d.\*):** Available in configurations from 4x20 GB to 48x240 GB
- **Flex profiles (bxf.\*):** Available in configurations from 4x16 GB to 48x192 GB
- Each host flavor defines both CPU and memory allocation for your member nodes

**Maxmemory configuration:** Your deployment is configured with `maxmemory` set to 80% of the node's available memory. When scaling up memory to accommodate more data, you might also want to [adjust the `maxmemory` setting](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-changing-configuration) to optimize resource utilization.

**Cache-only deployments:** If you [configured Redis as a cache](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-redis-cache) with persistence disabled, you can scale memory to the amount that best fits your caching needs without concern for persistent storage requirements.

### vCPU
{: #resources-scaling-cores}

If you find that your database workloads need more CPU resources, you can scale the amount of CPU allocated to your service. Select the host flavor that matches your resource needs. Redis offers Isolated Compute profiles (bx3d.\*) and Flex profiles (bxf.\*), each providing different CPU and memory configurations.

## Scaling considerations
{: #resources-scaling-consider}

- Scaling your deployment up might cause your databases to restart. If your deployment needs to be moved to a host with more capacity then, the databases are restarted as part of the move.
- Scaling down RAM or CPU does not trigger database restarts.
- Disk cannot be scaled down.
- Scaling between host flavor types (Isolated Compute and Flex profiles) moves your deployment to new hosts. Your databases are restarted as part of that move. As your deployment is moved to a new host, this can also take longer than just adding more resources.
- Similarly, drastically increasing CPU, RAM, or disk can take longer than smaller increases to account for provisioning more underlying hardware resources.
- Scaling operations are logged in [{{site.data.keyword.atracker_full}}](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-at_events).
- For Redis databases, memory, and CPU are adjusted together by selecting a host flavor. Disk is scaled separately from CPU and memory.

Scaling provides a way to restart your {{site.data.keyword.databases-for-redis}} instances as a side effect of resource changes. {{site.data.keyword.cloud}} support does not support ad-hoc restart requests.
{: note}

## Scaling in the UI
{: #resources-scaling-ui}
{: ui}

In the **Resources** tab of the UI, you'll find both "Hosting model" and "Resource allocations" tiles. These tiles reflect your current resources and hosting model. Select **Configure** on the Resource allocations tile. This opens up a panel where you can adjust your resources.

The "Host sizes" table is where you can select the vCPU and RAM configuration per member for your database. Available host flavors include Isolated Compute profiles (bx3d.\*) and Flex profiles (bxf.\*), each offering different CPU and memory combinations.

The "Disk (GB/member)" slider is your disk selection per member. Drag the slider or adjust the number in the input box to change the number of GB disk. Note that Disk is tied to IOPS at 1 GB = 10 IOPS.

Members is the number of members of your database. For Redis, members are set to 2. Review your total estimated cost in our calculator at the end.

After you are done, click **Apply changes** to trigger the scaling operation.

## Switching hosting models in the UI
{: #resources-switching-ui}
{: ui}

In the Resources tab of the UI, select **Configure** on the Hosting model tile. This opens up a panel where you can adjust your hosting model selection.

The first option available is "Select your hosting model". Here, you can switch between Isolated Compute profiles (bx3d.\*) and Flex profiles (bxf.\*).

Next, you see the options to also adjust the resources of the new hosting model you've selected. Complete the instructions in [Scaling in the UI](#resources-scaling-ui) to adjust your resources.

Click **Apply changes** to trigger this scale operation.

## Resources and scaling in the CLI
{: #resources-scaling-cli}
{: cli}

Use the following command to review the resources on your deployment:

```sh
ibmcloud resource service-instance <INSTANCE_NAME_OR_CRN> -o JSON
```
{: pre}

Check for these values in the output:

```json
"parameters": {
    "dataservices": {
        "redis": {
            "host_flavor": "bx2.4x16",
            "members": 3,
            "storage_gb": 10,
            "version": "8.2"
        }
    }
}
```
{: codeblock}

Example output:

```sh
ibmcloud resource service-instance crn:v1:staging:public:databases-for-redis:ca-mon:a/cf8d4161fa0243b9a2a5494cd7ff66b7:36310556-f628-4dac-8323-6088a6758658:: -o JSON
```
{: pre}

```json
[
    {
        "guid": "36310556-f628-4dac-8323-6088a6758658",
        "id": "crn:v1:staging:public:databases-for-redis:ca-mon:a/cf8d4161fa0243b9a2a5494cd7ff66b7:36310556-f628-4dac-8323-6088a6758658::",
        "name": "Redis-x3-02jun26",
        "region_id": "ca-mon",
        "resource_plan_id": "databases-for-redis-gen2-standard",
        "parameters": {
            "dataservices": {
                "redis": {
                    "host_flavor": "bx2.4x16",
                    "members": 3,
                    "storage_gb": 10,
                    "version": "8.2"
                }
            }
        },
        "state": "provisioning"
    }
]
```
{: codeblock}

### Scaling the host flavor and disk storage
{: #scaling-hostflavor-disk-cli}
{: cli}

Choose the required host flavor and disk value for your deployment. Use the following command to scale the host flavor and disk storage:

```sh
ibmcloud resource service-instance-update <INSTANCE_NAME_OR_CRN> --service-plan-id databases-for-redis-gen2-standard -p '{"dataservices": {"redis": {"storage_gb":20,"host_flavor":"bx2.4x16"}}}' -g Default
```
{: pre}

### Available host flavors
{: #available-hostflavors-cli}
{: cli}

For information about available host flavors and flex and fixed profiles, see [Gen 2 isolated compute](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-isolated-compute&interface=cli).

**Disk storage range**

| Parameter | Value |
|-----------|-------|
| Minimum | 10 GB per member |
| Maximum | 4000 GB per member |
{: caption="Disk storage limits" caption-side="bottom"}

## Resources and scaling in the API
{: #resources-scaling-api}
{: api}

Use the following command to review the resources on your deployment:

```sh
IAM_TOKEN=$(ibmcloud iam oauth-tokens -o json | jq .iam_token -r)
curl -X GET resource-controller.cloud.ibm.com/v2/resource_instances/<INSTANCE_GUID> -H "Authorization: ${IAM_TOKEN}" | jq
```
{: pre}

Check for these values in the output:

```json
"parameters": {
    "dataservices": {
        "redis": {
            "host_flavor": "bx2.4x16",
            "members": 3,
            "storage_gb": 10,
            "version": "8.2"
        }
    }
}
```
{: codeblock}

Example output:

```json
{
  "id": "crn:v1:staging:public:databases-for-redis:ca-mon:a/cf8d4161fa0243b9a2a5494cd7ff66b7:36310556-f628-4dac-8323-6088a6758658::",
  "guid": "36310556-f628-4dac-8323-6088a6758658",
  "name": "Redis-x3-02jun26",
  "region_id": "ca-mon",
  "resource_plan_id": "databases-for-redis-gen2-standard",
  "parameters": {
    "dataservices": {
      "redis": {
        "host_flavor": "bx2.4x16",
        "members": 3,
        "storage_gb": 10,
        "version": "8.2"
      }
    }
  },
  "state": "provisioning"
}
```
{: codeblock}

### Scaling the host flavor and disk storage
{: #scaling-hostflavor-disk-api}
{: api}

Choose the required host flavor and disk value for your deployment. Use the following command to scale the host flavor and disk storage:

```sh
curl -X PATCH resource-controller.cloud.ibm.com/v2/resource_instances/<INSTANCE_GUID> -H "Authorization: ${IAM_TOKEN}" -H 'Content-Type: application/json' -d '{"parameters":{"dataservices":{"redis":{"host_flavor":"bx2.4x16"}}}}' | jq
```
{: pre}

Example scaling to a different host flavor:

```sh
curl -X PATCH resource-controller.cloud.ibm.com/v2/resource_instances/<INSTANCE_GUID> -H "Authorization: ${IAM_TOKEN}" -H 'Content-Type: application/json' -d '{"parameters":{"dataservices":{"redis":{"host_flavor":"bx3d.4x20"}}}}' | jq
```
{: pre}

### Available host flavors
{: #available-hostflavors-api}
{: api}

For information about available host flavors and flex and fixed profiles, see [Gen 2 isolated compute](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-isolated-compute&interface=api).

**Disk storage range**

| Parameter | Value |
|-----------|-------|
| Minimum | 10 GB per member |
| Maximum | 4000 GB per member |
{: caption="Disk storage limits" caption-side="bottom"}

## Resources and scaling with Terraform
{: #resources-scaling-terraform}
{: terraform}

Use the following command to review the resources on your deployment:

```terraform
data "ibm_resource_group" "group" {
  name = "<your_group>"
}

resource "ibm_resource_instance" "<your_database>" {
  name = "<your_database_name>"
  plan = "databases-for-redis-gen2-standard"
  service = "databases-for-redis"
}
```
{: codeblock}

### Scaling the host flavor and disk storage
{: #scaling-hostflavor-disk-terraform}
{: terraform}

Choose the required host flavor and disk value for your deployment. Use the following configuration to scale the host flavor and disk storage:

```terraform
data "ibm_resource_group" "group" {
  name = "<your_group>"
}

resource "ibm_resource_instance" "<your_database>" {
  name = "<your_database_name>"
  plan = "databases-for-redis-gen2-standard"
  service = "databases-for-redis"
  location = "us-east"
  tags = ["tag1","tag2"]
  resource_group_id = data.ibm_resource_group.group.id

  parameters_json = jsonencode({
    "dataservices": {
      "redis": {
        "host_flavor": "bx2.8x32",
        "storage_gb": 60
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

Before running a Terraform script on an existing instance, use the `terraform plan` command to compare the current infrastructure state with the desired state defined in your Terraform files. Any alteration to the `resource_group_id`, `service plan`, `version`, `key_protect_instance`, `key_protect_key`, and `backup_encryption_key_crn` attributes recreates your instance. For a list of current argument references with the `Forces new resource` specification, see the [ibm_database Terraform Registry](https://registry.terraform.io/providers/IBM-Cloud/ibm/latest/docs/resources/database){: external}.
{: important}

### Available host flavors
{: #available-hostflavors-terraform}
{: terraform}

For information about available host flavors and flex and fixed profiles, see [Gen 2 isolated compute](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-isolated-compute&interface=terraform).

**Disk storage range**

| Parameter | Value |
|-----------|-------|
| Minimum | 10 GB per member |
| Maximum | 4000 GB per member |
{: caption="Disk storage limits" caption-side="bottom"}
