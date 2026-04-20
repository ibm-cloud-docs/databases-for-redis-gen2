---
copyright:
  years: 2026
lastupdated: "2026-04-20"

keywords: acl, access control list, connection strings, admin, service credentials, new user, admin password, default user, rbac

subcollection: databases-for-redis-gen2

---

{{site.data.keyword.attribute-definition-list}}

# Managing users and roles
{: #user-management}

{{site.data.keyword.databases-for-redis_full}} instances come with authentication enabled and use Redis's built-in access control.They support multiple users and authentication through  [Access Control List (ACL) support](https://redis.io/topics/acl){: external}. 


## Managing Redis users
{: #user-management-users}

### The admin user
{: #admin-user}

When you provision a new instance in {{site.data.keyword.cloud_notm}}, you are automatically given access to the Admin user. You can also create additional users and credentials for your instance.

To use the Admin user to connect to your instance, first set the Admin password.

#### Setting the admin password in the UI
{: #user-management-set-admin-password-ui}
{: ui}

Set your Admin password through the UI by selecting your instance from the Resource List in the [{{site.data.keyword.cloud_notm}} Dashboard](https://cloud.ibm.com/){: external}. Then, select **Settings**. Next, select *Change Database Admin Password*.

#### Setting the admin password in the CLI
{: #user-management-set-admin-password-cli}
{: cli}

Use the `cdb user-password` command from the [{{site.data.keyword.databases-for}} CLI plug-in](/docs/databases-cli-plugin) to set the `admin` password.

For example, to set the admin password for your instance, use the following command:

```sh
ibmcloud cdb user-password <INSTANCE_NAME_OR_CRN> admin <NEWPASSWORD>
```
{: pre}

#### Setting the admin password in the API
{: #user-management-set-admin-password-api}
{: api}

The Foundation Endpoint that is shown in the Overview Deployment Details section of your service provides the base URL to access this instance through the API. Use it with the [Set specified user's password](/apidocs/cloud-databases-api/cloud-databases-api-v5#updateuser){: external} endpoint to set the admin password.

```sh
curl -X PATCH `https://api.{region}.databases.cloud.ibm.com/v5/ibm/deployments/{id}/users/admin` \
-H `Authorization: Bearer <>` \
-H `Content-Type: application/json` \
-d `{"password":"newrootpasswordsupersecure21"}` \
```
{: pre}

### The `default` user
{: #redis-default-user}

{{site.data.keyword.databases-for-redis}} no longer uses the `default` user internally. Instead, {{site.data.keyword.databases-for-redis}} instances are managed by the {{site.data.keyword.databases-for}} `ibm-user`.

If your instances currently use the `default` user, it's possible to continue doing so. However, continued usage of the `default` user requires a password change, which is expected to limit permissions. These permission limitations are an expected behavioral change as part of a major version upgrade. Specifically, the following permissions are restricted for the `default` user:

- `config`: The `default` user cannot view, add, update, or delete database configurations. The `default` user also cannot create or manage database users and roles.
- `acl`: The `default` user cannot create new users.

If you plan to continue using the default user, ensure you test your applications thoroughly after updating the password for compatibility.
For more information on configuring access and permissions in Redis, see [ACL support in Redis](https://redis.com/blog/getting-started-redis-6-access-control-lists-acls/).

To update the `default` user password, use a command like:

```sh
ibmcloud cdb deployment-user-password <INSTANCE_NAME_OR_CRN> default <NEW PASSWORD>
```
{: pre}

For more information, see [Upgrading to a new major version](/docs/databases-for-redis-gen2?topic=databases-for-redis-upgrading){: external}.

## Managing Redis roles
{: #user-management-roles}

### Role-based access control (RBAC)
{: #redis-rbac}

Role-based access control (RBAC) allows you to configure the level of access each user has.

{{site.data.keyword.databases-for-redis}} currently supports the following roles:

- The `admin` role provides full control and access to all admin commands and operations.
- The `all` role provides `admin`, `read` and `write` access, giving users full control over all commands and operations.
- The `read` role allows read-only access to commands. Users with this role can run read operations but cannot perform write operations.
- The `write` role allows write-only access to commands. Users with this role can run write operations but cannot perform read operations.

The admin commands `config get`, `config reset`, `acl whoami`, `acl cat`, `acl users`, `acl genpass`, `acl log`, and `acl help` are available for use in `admin` and `all` but all other `acl` and `config` commands are not.

These roles can also be combined to configure a user's level of access.

#### RBAC role combinations
{: #redis-rbac-combos}

`+` includes commands

`-` excludes commands

`+@` includes command categories

`-@` excludes command categories

- `admin` + `read`: Full `admin` control with the ability to run `read` operations.
- `admin` + `write`: Full `admin` control with the ability to run `write` operations.
- `admin` + `all`: Simplifies to `all`, which includes `admin`, `read`, and `write`.
- `read` + `write`: Users with both `read` and `write` roles can perform both `read` and `write` operations, but they won't have administrative privileges.
- `read` + `all`: Simplifies to `all`, which includes `admin`, `read`, and `write`.
- `write` + `all`: Simplifies to `all`, which includes `admin`, `read`, and `write`.
- `admin` + `read` + `write`: Full `admin` control with the ability to run both `read` and `write` operations.
- `admin` + `read` + `all`: Simplifies to `all`, which includes `admin`, `read`, and `write`.
- `admin` + `write` + `all`: Simplifies to `all`, which includes `admin`, `read`, and `write`.
- `read` + `write` + `all`: Simplifies to `all`, which includes `admin`, `read`, and `write`.

These combinations provide different levels of access control. Choose the combination that aligns with your specific requirements and security considerations.

### Create a user with RBAC roles through the API
{: #redis-rba-create-user-api}
{: api}

To create a user using RBAC roles, use the following command:

```sh
curl -X POST "https://{region}.databases.cloud.ibm.com/v5/ibm/deployments/{deployment_id}/users/database" \
     -H "Authorization: Bearer {iam_token}" \
     -H "Content-Type: application/json" \
     -d '{
           "user": {
             "username": "example-user",
             "password": "YourPassword123!"
           }
         }'
```
{: pre}

To create a user with default role, use the following command:

```sh
curl -X POST "https://{region}.databases.cloud.ibm.com/v5/ibm/deployments/{deployment_id}/users/database" \
     -H "Authorization: Bearer {iam_token}" \
     -H "Content-Type: application/json" \
     -d '{
           "user": {
             "username": "example-user",
             "password": "YourPassword123!"
           }
         }'
```
{: pre}

To change or update a role, use the following command:

```sh
curl -X PATCH "https://{region}.databases.cloud.ibm.com/v5/ibm/deployments/{deployment_id}/users/database/{username}" \
     -H "Authorization: Bearer {iam_token}" \
     -H "Content-Type: application/json" \
     -d '{
           "user": {
             "role": "+@read +@admin"
           }
         }'
```
{: pre}

To change password of a role, use the following command:

```sh
curl -X PATCH "https://{region}.databases.cloud.ibm.com/v5/ibm/deployments/{deployment_id}/users/database/{username}" \
     -H "Authorization: Bearer {iam_token}" \
     -H "Content-Type: application/json" \
     -d '{
           "user": {
             "password": "NewSecurePassword456!"
           }
         }'
```
{: pre}

To delete a user, use the following command:

```sh
curl -X DELETE "https://{region}.databases.cloud.ibm.com/v5/ibm/deployments/{deployment_id}/users/database/{username}" \
     -H "Authorization: Bearer {iam_token}" \
     -H "Accept: application/json"
```
{: pre}

### Create a user with RBAC roles through the CLI
{: #redis-rba-create-user-cli}
{: cli}

To create a user using RBAC roles, use the following command:

```sh
ibmcloud cdb user-create <CRN> <USERNAME> <PASSWORD> -r "<ROLE_STRING>"
```
{: pre}

For `-r USER_ROLE`, use some combination of `<+/-@all>` `<+/-@read>` `<+/-@write>` `<+/-@admin>` to grant access.

Example:

```sh
ibmcloud cdb user-create \
  crn:v1:bluemix:public:databases-for-redis-dev-yp-03:us-south:a/40ddc34a953a8c02f10987b59085b60e:7065adf2-cc4f-433a-9a97-aa69d03f6f45:: \
  ibm_cloud_35ea37ad_119b_462d_bfd4_a4f001387cdf \
  Password1234567890 \
  -r "+@read +@write"
```
{: pre}

Sample output:

```sh

Key                   Value
ID                    crn:v1:bluemix:public:databases-for-redis-dev-yp-03:us-south:a/40ddc34a953a8c02f10987b59085b60e:7065adf2-cc4f-433a-9a97-aa69d03f6f45:task:49ec86e6-a2ac-4eb2-9c6b-48039c554f7e
Deployment ID         crn:v1:bluemix:public:databases-for-redis-dev-yp-03:us-south:a/40ddc34a953a8c02f10987b59085b60e:7065adf2-cc4f-433a-9a97-aa69d03f6f45::
Description           Creating user
Created At            2025-04-18T07:26:31Z
Status                running
Progress Percentage   0
...
Progress Percentage   66
...
Status                completed
Progress Percentage   100
Location              https://api.dev-yp-03.us-south.databases.cloud.ibm.com/v5/ibm/deployments/crn:v1:bluemix:public:databases-for-redis-dev-yp-03:us-south:a%2F40ddc34a953a8c02f10987b59085b60e:7065adf2-cc4f-433a-9a97-aa69d03f6f45::
OK
```
{: pre}

To create a user with default role, use the following command:

```sh
ibmcloud cdb user-create <CRN> <USERNAME> <PASSWORD>
```
{: pre}

Example:

```sh
ibmcloud cdb user-create \
  crn:v1:bluemix:public:databases-for-redis-dev-yp-03:us-south:a/40ddc34a953a8c02f10987b59085b60e:7065adf2-cc4f-433a-9a97-aa69d03f6f45:: \
  ibm_cloud_user_default_role \
  Password1234567890
```
{: pre}

Sample output:

```sh

Key                   Value
ID                    crn:v1:bluemix:public:databases-for-redis-dev-yp-03:us-south:a/40ddc34a953a8c02f10987b59085b60e:7065adf2-cc4f-433a-9a97-aa69d03f6f45:task:f225e6a3-3f4e-449d-bfce-a5e171719074
Deployment ID         crn:v1:bluemix:public:databases-for-redis-dev-yp-03:us-south:a/40ddc34a953a8c02f10987b59085b60e:7065adf2-cc4f-433a-9a97-aa69d03f6f45::
Description           Creating user
Created At            2025-04-18T07:47:27Z
Status                running
Progress Percentage   0

Status                completed
Progress Percentage   100
Location              https://api.dev-yp-03.us-south.databases.cloud.ibm.com/v5/ibm/deployments/crn:v1:bluemix:public:databases-for-redis-dev-yp-03:us-south:a%2F40ddc34a953a8c02f10987b59085b60e:7065adf2-cc4f-433a-9a97-aa69d03f6f45::
OK
```
{: pre}

To change or update a role, use the following command:

```sh
ibmcloud cdb redis user-set <CRN> <USERNAME> <ROLE_STRING>
```
{: pre}

Example:

```sh
ibmcloud cdb redis user-set \
  crn:v1:bluemix:public:databases-for-redis-dev-yp-03:us-south:a/40ddc34a953a8c02f10987b59085b60e:7065adf2-cc4f-433a-9a97-aa69d03f6f45:: \
  ibm_cloud_35ea37ad_119b_462d_bfd4_a4f001387cdf \
  "+@all"
```
{: pre}

Sample output:

```sh

Key                   Value
ID                    crn:v1:bluemix:public:databases-for-redis-dev-yp-03:us-south:a/40ddc34a953a8c02f10987b59085b60e:7065adf2-cc4f-433a-9a97-aa69d03f6f45:task:3a30ebc4-20d8-4004-8514-47c615276f81
Deployment ID         crn:v1:bluemix:public:databases-for-redis-dev-yp-03:us-south:a/40ddc34a953a8c02f10987b59085b60e:7065adf2-cc4f-433a-9a97-aa69d03f6f45::
Description           Updating user
Created At            2025-04-18T07:32:40Z
Status                running
Progress Percentage   0

Status                completed
Progress Percentage   100
Location              https://api.dev-yp-03.us-south.databases.cloud.ibm.com/v5/ibm/deployments/crn:v1:bluemix:public:databases-for-redis-dev-yp-03:us-south:a%2F40ddc34a953a8c02f10987b59085b60e:7065adf2-cc4f-433a-9a97-aa69d03f6f45::
OK
```
{: pre}

To change the password of a role, use the following command:

```sh
ibmcloud cdb user-password <CRN> <USERNAME> <NEW_PASSWORD>
```
{: pre}

Example:

```sh
ibmcloud cdb user-password \
  crn:v1:bluemix:public:databases-for-redis-dev-yp-03:us-south:a/40ddc34a953a8c02f10987b59085b60e:3b20b985-1c89-441e-8e00-2d394d441a02:: \
  ibm_cloud_35ea37ad_119b_462d_bfd4_a4f001387cdf \
  Password1234567890123
```

Sample output:

```sh

Key                   Value
ID                    crn:v1:bluemix:public:databases-for-redis-dev-yp-03:us-south:a/40ddc34a953a8c02f10987b59085b60e:3b20b985-1c89-441e-8e00-2d394d441a02:task:c254ccef-9e9c-4375-ae0f-4c78173f6278
Deployment ID         crn:v1:bluemix:public:databases-for-redis-dev-yp-03:us-south:a/40ddc34a953a8c02f10987b59085b60e:3b20b985-1c89-441e-8e00-2d394d441a02::
Description           Updating user
Created At            2025-04-18T07:41:32Z
Status                running
Progress Percentage   0

Progress Percentage   66

Status                completed
Progress Percentage   100
Location              https://api.dev-yp-03.us-south.databases.cloud.ibm.com/v5/ibm/deployments/crn:v1:bluemix:public:databases-for-redis-dev-yp-03:us-south:a%2F40ddc34a953a8c02f10987b59085b60e:3b20b985-1c89-441e-8e00-2d394d441a02::
OK
```
{: pre}

To delete a role, use the following command:

```sh
ibmcloud cdb user-delete <CRN> <USERNAME>
```
{: pre}

Example:

```sh
ibmcloud cdb user-delete \
  crn:v1:bluemix:public:databases-for-redis-dev-yp-03:us-south:a/40ddc34a953a8c02f10987b59085b60e:3b20b985-1c89-441e-8e00-2d394d441a02:: \
  ibm_cloud_35ea37ad_119b_462d_bfd4_a4f001387cdf
```
{: pre}

Sample output:

```sh

Key                   Value
ID                    crn:v1:bluemix:public:databases-for-redis-dev-yp-03:us-south:a/40ddc34a953a8c02f10987b59085b60e:3b20b985-1c89-441e-8e00-2d394d441a02:task:cc7effad-4fb2-4ae6-bd94-2d432b471e39
Deployment ID         crn:v1:bluemix:public:databases-for-redis-dev-yp-03:us-south:a/40ddc34a953a8c02f10987b59085b60e:3b20b985-1c89-441e-8e00-2d394d441a02::
Description           Deleting user
Created At            2025-04-18T07:44:10Z
Status                running
Progress Percentage   0

Status                completed
Progress Percentage   100
Location              https://api.dev-yp-03.us-south.databases.cloud.ibm.com/v5/ibm/deployments/crn:v1:bluemix:public:databases-for-redis-dev-yp-03:us-south:a%2F40ddc34a953a8c02f10987b59085b60e:3b20b985-1c89-441e-8e00-2d394d441a02::
OK
```
{: pre}

### Create a user with RBAC roles through Terraform
{: #redis-rba-create-user-tf}
{: terraform}

To create a formation with a user using RBAC roles, use the following command:

```terraform
resource "ibm_database" "redis" {
  name              = "Databases for Redis-jt-DO-NOT-DELETE"
  plan              = "standard"
  location          = var.region
  service           = "databases-for-redis"
  version           = "7.2"
  service_endpoints = "public-and-private"
  resource_group_id = data.ibm_resource_group.group.id
  tags              = ["redis", "user-mgmt"]

#   ##################################################
#   # 1. Create user with custom ACL role
#   ##################################################
  users {
    name     = "reader-user-with-role"
    password = "secureReadPass123456789"
    role     = "-@all +@read"
  }
```
{: pre}

For `role`, use some combination of `<+/-@all>` `<+/-@read>` `<+/-@write>` `<+/-@admin>` to grant  access.

To create a formation with a default role, use the following command:

```sh
resource "ibm_database" "redis" {
  name              = "Databases for Redis-jt-DO-NOT-DELETE"
  plan              = "standard"
  location          = var.region
  service           = "databases-for-redis"
  version           = "7.2"
  service_endpoints = "public-and-private"
  resource_group_id = data.ibm_resource_group.group.id
  tags              = ["redis", "user-mgmt"]


  ##################################################
  # 2. Create user with default role
  ##################################################
  users {
    name     = "default-user-role"
    password = "defaultPass123456789"
    # no `role` field → default ACL applies
  }
```
{: pre}

Users can be configured during the initial Terraform provisioning with either default or custom roles. After provisioning, operations, such as creating, deleting, updating passwords, or changing roles are not supported to maintain consistency and security.
{: note}

## Redis roles
{: #redis-roles}

The Admin user and all other users on your instance have full access to the set of Redis commands, except for the subcommand `config` and `acl` - this includes the Admin user. `config get`, `config reset`, `acl whoami`, `acl cat`, `acl users`, `acl genpass`, `acl log`, and `acl help` are useable.

In Redis, any user that you create, whether through *Service Credentials*, the CLI, API, or directly in Redis, has the same access. You cannot use Redis itself to create users or roles with access that is limited to specific keys or ranges of keys, as they are not propagated automatically in a cluster deployment. All other means to manage users ensure propagation across the cluster.

### Creating users through the UI
{: #create-users-service-cred}
{: ui}

1. Navigate to the service dashboard for your service.
2. Select *Service Credentials*.
3. Select *New Credential*.
4. Choose a descriptive name for your new credential.
5. (Optional) Specify whether the new credentials use a public or private endpoint. Use either `{ "service-endpoints": "public" }` / `{ "service-endpoints": "private" }` in the *Add Inline Configuration Parameters* field to generate connection strings using the specified endpoint. Use of the endpoint is not enforced. It just controls which hostnames are in the connection strings. Public endpoints are generated by default.
6. Click *Add* to provision the new credentials. A username, password, and an associated user are auto-generated.

The new credentials appear in the table, and the connection strings are available as JSON in a click-to-copy field under *View Credentials*.

### Creating users from the API
{: #create-users-api}
{: api}

The _Foundation Endpoint_ that is shown in the _Overview_ of your service provides the base URL to access this instance through the API. To create and manage users, use the base URL with the [users](/apidocs/cloud-databases-api/cloud-databases-api-v5#updateuser) endpoint:

```sh
curl -X POST https://api.{region}.databases.cloud.ibm.com/v5/ibm/deployments/{id}/users/{user_type} \
-H "Authorization: Bearer $APIKEY" \ 
-H "Content-Type: application/json" \
-d "{"user": {"username": "user", "password": "v3ry-1-secUre-pAssword-2"}}" \
```
{: pre}

To retrieve a user's connection strings, use the base URL with the `/users/{userid}/connections` endpoint.

## Internal-use users 
{: #internal-users}

There are four reserved users on your instance. Modifying these users causes your instance to become unstable or unusable.

- `ibm-user` - An internal `admin` user for managing the instance and exposing metrics.
- `replication-user` - The user account that is used for replication.
- `sentinel-user` - The user account for sentinels to handle monitoring and failovers.
- `admin` - The default user provided to access your instance.
