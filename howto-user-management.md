---
copyright:
  years: 2026
lastupdated: "2026-11-04"

keywords: manager, roles, service credentials, redis users, redis service credentials, connection strings, manager password, new user, Gen 2, ACL, ACL restrictions

subcollection: databases-for-redis-gen2

---

{{site.data.keyword.attribute-definition-list}}

# Managing users, roles, and privileges
{: #user-management}

[Gen 2]{: tag-purple}

{{site.data.keyword.databases-for-redis}} deployments no longer include a default `admin` user. Instead, you can create users with the `Manager` or `Writer` role using the {{site.data.keyword.cloud}} service credential interface using the UI, CLI, or API.

## The manager user
{: #user-manager}

The `manager` user functions as a power user with extensive data access but limited administrative capabilities. The `manager` role provides:

- **Full data access**: Complete read and write permissions for all keys.
- **Limited config access**: Can view configuration (`config|get`) and reset statistics (`config|resetstat`).
- **User management**: Can create, update, and delete users with `ACL SETUSER` and `ACL DELUSER`, and can view ACL information. For more information, see [Restrictions on ACL commands](#user-management-acl-protection).
- **No dangerous commands**: Cannot execute shutdown, replication, or debug commands.

## The writer user
{: #user-writer}

The `writer` user is the default role for Redis deployments, providing standard read and write access for application workloads. The `writer` role is designed for typical database operations without administrative privileges. The `writer` role provides:

- **Full read access**: Can read all keys and execute all read commands.
- **Full write access**: Can create, update, and delete data across all keys.
- **Connection management**: Can manage client connections and pub/sub operations.
- **Key operations**: Can list, scan, and inspect keys.
- **No administrative access**: Cannot view or modify configuration, ACL, or system settings.
- **No dangerous commands**: Cannot execute shutdown, replication, debug, or monitoring commands.

## Creating users in the UI
{: #user-management-creating-users-service-cred}
{: ui}

1. Go to the service dashboard for your service.
2. Click **Service credentials** to open **Service credentials**.
3. Click **New credential**.
4. Choose a descriptive name for your new credential.
5. Choose the user role: Writer or Manager.
6. Click **Add** to provision the new credentials. A username and password, and an associated Redis user is auto-generated.
7. The new credentials along with the connection strings details are available as JSON in a click-to-copy field under **Credentials successfully created** pop-up window.

This is on one-time view basis. Copy and save the credentials and connection string details securely.
{: note}

## Deleting the user in the UI
{: #user-management-delete-user-ui}
{: ui}

You can delete the user by clicking **Delete** button using the row actions option available at the end of each user row.

## Creating users in the CLI
{: #user-management-creating-users-cli}
{: cli}

Use the following command from the {{site.data.keyword.cloud_notm}} CLI to create users:

```sh
ibmcloud resource service-key-create NAME [ROLE_NAME] ( --instance-id SERVICE_INSTANCE_ID | --instance-name SERVICE_INSTANCE_NAME)
```
{: pre}

Where:
- **NAME**: a descriptive user name
- **ROLE_NAME**: can be either Manager or Writer
- **SERVICE_INSTANCE_ID**: GUID of the service instance
- **SERVICE_INSTANCE_NAME**: name of the service instance

For example:

```sh
ibmcloud resource service-key-create test-user-writer --instance-id f0c3b472-0ae9-4eef-a63d-5233dbe348ef
```
{: pre}

By default, the writer role is assigned to `test-user-writer` user because the ROLE_NAME is not specified explicitly in the given example.

## Deleting the user in the CLI
{: #user-management-delete-manager-user-cli}
{: cli}

Use the following command from the {{site.data.keyword.cloud_notm}} CLI {{site.data.keyword.databases-for}} plug-in to delete the created user:

```sh
ibmcloud resource service-key-delete <service_key_name>
```
{: pre}

## Creating users in the API
{: #user-management-creating-users-api}
{: api}

Use the following command to create users using the API:

```sh
IAM_TOKEN=$(ibmcloud iam oauth-tokens -o json | jq .iam_token -r)
curl -s -X POST "resource-controller.cloud.ibm.com/v2/resource_keys" \
  -H "Authorization: ${IAM_TOKEN}" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "NAME",
    "source": "SERVICE_INSTANCE_ID",
    "role": "ROLE_NAME"
  }' | jq '.'
```
{: pre}

Where:
- **NAME**: a descriptive user name
- **ROLE_NAME**: can be either Manager or Writer
- **SERVICE_INSTANCE_ID**: GUID of the service instance

For example:

```sh
curl -s -X POST "resource-controller.cloud.ibm.com/v2/resource_keys" \
  -H "Authorization: ${IAM_TOKEN}" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "redis-user-mk",
    "source": "f0c3b472-0ae9-4eef-a63d-5233dbe348ef",
    "role": "Manager"
  }' | jq '.'
```
{: pre}

## Deleting the user in the API
{: #user-management-delete-user-api}
{: api}

Use the following command to delete the user using the API:

```sh
curl -X DELETE resource-controller.cloud.ibm.com/v2/resource_keys/<SERVICE-INSTANCE-GUID> -H "Authorization: $IAM_TOKEN"
```
{: pre}

## Creating users in Terraform
{: #user-management-creating-users-terraform}
{: terraform}

Use the following command to create users through Terraform:

```terraform
data "ibm_resource_group" "group" {
  name = "<your_resource_group>"
}
resource "ibm_resource_key" "resourceKey" {
  name                 = "<user-name>"
  role                 = "<ROLE_NAME>"
  resource_instance_id = "<SERVICE_INSTANCE_ID>"
}
```
{: codeblock}

where:
- **user-name**: a descriptive user name
- **ROLE_NAME**: can be either Manager or Writer
- **SERVICE_INSTANCE_ID**: GUID of the service instance

## Deleting the user in Terraform
{: #user-management-delete-user-terraform}
{: terraform}

To delete resources managed by Terraform, run the command `terraform destroy`:

- When Terraform creates a user, it automatically creates a **state file (terraform.tfstate)** and the details get added in the resources of the state file. This file tracks all managed resources and is used by Terraform to identify what to destroy during deletion.

- Terraform can only delete resources that it created. Resources provisioned through the UI, CLI, or API cannot be destroyed by Terraform **unless they have first been imported into the Terraform state**.

- All entries present in the resources of the state file will get deleted when you run destroy. Therefore ensure the resources that you want to delete are the only ones in the resources list before running the `terraform destroy` command.

## Restrictions on ACL commands
{: #user-management-acl-protection}

Users with the `Manager` role can manage other users by running `ACL` commands, such as `ACL SETUSER` and `ACL DELUSER`. To prevent privilege escalation, {{site.data.keyword.databases-for-redis}} checks each `ACL` command before it runs. Commands that grant administrative privileges or modify reserved users are blocked. This protection is enabled by default in {{site.data.keyword.databases-for-redis}} 8.2 and later. No installation or configuration is required.

This protection affects only `ACL` commands. Reads, writes, and all other commands continue to run normally.
{: note}

### ACL command permissions by role
{: #user-management-acl-protection-roles}

- Users with the `Writer` role can't run `ACL` commands.
- Users with the `Manager` role can run `ACL SETUSER`, `ACL DELUSER`, `ACL GETUSER`, `ACL LIST`, `ACL USERS`, `ACL WHOAMI`, `ACL CAT`, `ACL GENPASS`, `ACL LOG`, and `ACL HELP`. Other `ACL` subcommands, including `ACL LOAD`, `ACL SAVE`, and `ACL DRYRUN`, are not available.

### What is blocked
{: #user-management-acl-protection-blocks}

The following commands are blocked, even for users with the `Manager` role.

| Command | Blocked when |
|---------|--------------|
| `ACL SETUSER` | The target is a [reserved user](#internal-users), or the resulting permissions grant administrative access. |
| `ACL DELUSER` | The command includes one or more reserved users. |
{: caption="Table 1. ACL commands that are blocked" caption-side="bottom"}

For `ACL SETUSER`, the permissions are evaluated from left to right. The command is blocked if the resulting permissions grant administrative access. For example, nocommands +@all is blocked. The following permissions are blocked:

- `+@admin`
- `+@all`
- `allcommands`
- Any individual administrative command, such as `+CONFIG`, `+SHUTDOWN`, `+DEBUG`, `+MONITOR`, `+MODULE`, or `+REPLICAOF`

Permissions that remove access, such as `-@admin`, `-@all`, and `nocommands`, are allowed, unless the target is a reserved user.

Reserved users can't be modified or deleted, even to reduce their permissions. The reserved users are `default`, `ibm-user`, `monitoring-user`, `replication-user`, and `sentinel-user`. User names are case-sensitive.
{: important}

### Error messages
{: #user-management-acl-protection-errors}

When a command is blocked, it isn't run and the client receives one of the following errors.

| Command | Error |
|---------|-------|
| `ACL SETUSER alice +@admin` | `ERR Permission denied: Cannot assign @admin category permissions` |
| `ACL SETUSER default +@read` | `ERR Permission denied: Cannot modify a protected internal user` |
| `ACL DELUSER replication-user` | `ERR Permission denied: Cannot delete a protected internal user` |
{: caption="Table 2. Error messages for blocked ACL commands" caption-side="bottom"}

### Verifying the restrictions
{: #user-management-acl-protection-verify}

Connect to your deployment with a user that has the `Manager` role, and then run the following commands in `redis-cli`.

1. Confirm that a non-administrative permission is allowed. The expected response is `OK`.

   ```sh
   ACL SETUSER testuser on >testpassword ~app:* +@read +@write
   ```
   {: pre}

2. Confirm that an administrative permission is blocked. The expected response is `ERR Permission denied: Cannot assign @admin category permissions`.

   ```sh
   ACL SETUSER testuser +@admin
   ```
   {: pre}

3. Confirm that a reserved user can't be modified. The expected response is `ERR Permission denied: Cannot modify a protected internal user`.

   ```sh
   ACL SETUSER default nocommands
   ```
   {: pre}

4. Delete the test user. The expected response is `(integer) 1`.

   ```sh
   ACL DELUSER testuser
   ```
   {: pre}

## Internal-use users
{: #internal-users}

There are five reserved users on your instance. Modifying these users causes your instance to become unstable or unusable.

- **default** Redis's built-in default user account
- **ibm-user** An internal user for managing the instance, exposing metrics, and API operations
- **monitoring-user** The user account that is used for monitoring
- **replication-user** The user account that is used for replication between member nodes
- **sentinel-user** The user account for sentinels to handle monitoring and failovers

Important notes:
- The five users (`default`, `ibm-user`, `monitoring-user`, `replication-user`, and `sentinel-user`) are strictly internal and cannot be modified.
- Attempting to create, delete, or modify these reserved users through the API will result in an error.
