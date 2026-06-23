---
copyright:
  years: 2026
lastupdated: "2026-06-23"

keywords: manager, roles, service credentials, redis users, redis service credentials, connection strings, manager password, new user, Gen 2

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

- **Full data access**: complete read and write permissions for all keys
- **Limited config access**: can view configuration (`config|get`) and reset statistics (`config|resetstat`)
- **Read-only ACL visibility**: can view ACL information but cannot create or manage users
- **No dangerous commands**: cannot execute shutdown, replication, or debug commands

## The writer user
{: #user-writer}

The `writer` user is the default role for Redis deployments, providing standard read and write access for application workloads. The `writer` role is designed for typical database operations without administrative privileges. The `writer` role provides:

- **Full read access**: Can read all keys and execute all read commands
- **Full write access**: Can create, update, and delete data across all keys
- **Connection management**: Can manage client connections and pub/sub operations
- **Key operations**: Can list, scan, and inspect keys
- **No administrative access**: Cannot view or modify configuration, ACL, or system settings
- **No dangerous commands**: Cannot execute shutdown, replication, debug, or monitoring commands

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

## Internal-use users
{: #internal-users}

There are five reserved users on your instance. Modifying these users causes your instance to become unstable or unusable.

- **default** Redis's built-in default user account
- **ibm-user** An internal user for managing the instance, exposing metrics, and API operations
- **replication-user** The user account that is used for replication between member nodes
- **sentinel-user** The user account for sentinels to handle monitoring and failovers


Important notes:
- The four users (`default`, `ibm-user`, `replication-user`, and`sentinel-user`) are strictly internal and cannot be modified.
- Attempting to create, delete, or modify these reserved users through the API will result in an error.
