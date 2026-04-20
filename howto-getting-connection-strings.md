---

copyright:
  years: 2026
lastupdated: "2026-04-20"

keywords: redis, databases, connection strings

subcollection: databases-for-redis-gen2

---

{{site.data.keyword.attribute-definition-list}}

# Getting connection strings
{: #connection-strings}

The {{site.data.keyword.databases-for-redis_full}} service is provisioned with authentication enabled. You need a username, password, and connection strings to connect and issue commands.

Connection Strings for your deployment are displayed on the Overview page, in the Endpoints panel.
All users on your deployment can use the connection strings, including connection strings for either public or private endpoints.

Your connection string will default to database `0`. However, modifying your connection to connect to a database other than `0` is supported. 
{: .note}

Your Redis deployment is provisioned with an admin user. You must set the admin password before you can use it to connect to the database. For more information, see the [Setting the Admin Password](/docs/databases-for-redis-gen2?topic=databases-for-redis-user-management&interface=ui#user-management-set-admin-password-ui) page.
{: .tip}

## Getting connection strings from the UI
{: #connection-strings-ui}
{: ui}

Follow these steps to retrieve your {{site.data.keyword.databases-for-redis}} instance connection strings:

1. In your deployment's **Overview page**, scroll down to the *Endpoints* section. 
1. Within the *Endpoints* section, you find a *Quick start tab* with the following sections: 
   - **Connect using a CLI** - This section contains information for connecting to your deployment through the [{{site.data.keyword.IBM_notm}} CLI](https://www.ibm.com/cloud/cli){: external}.
   - **Connect using a Redis Client** - This section allows you to get a TLS certificate and connect to your deployment.

## Getting connection strings from the CLI
{: #connection-strings-cli}
{: cli}

You can grab connection strings from the [CLI](/docs/databases-cli-plugin?topic=databases-cli-plugin-cdb-reference#deployment-connections).

```sh
ibmcloud cdb deployment-connections <INSTANCE_NAME_OR_CRN> -u <NEWUSERNAME> [--endpoint-type <endpoint type>]
```

Full connection information is returned by the `ibmcloud cdb deployment-connections` command with the `--all` flag. To retrieve all the connection information for your deployment, use the following command:

```sh
ibmcloud cdb deployment-connections <INSTANCE_NAME_OR_CRN> -u <NEWUSERNAME> --all [--endpoint-type <endpoint type>]
```

If you don't specify a user, the `deployment-connections` commands return information for the admin user by default. If you don't specify an endpoint type, the connection string returns the public endpoint by default. If your deployment has only a private endpoint, you must specify `--endpoint-type private` or the commands return an error. The user and endpoint type is not enforced. You can use any user on your deployment with either endpoint (if both exist on your deployment).

To use the `ibmcloud cdb` CLI commands, you must [install the {{site.data.keyword.databases-for}} plug-in](/docs/databases-cli-plugin?topic=databases-cli-plugin-cdb-reference).
{: .tip}

## Getting connection strings from the API
{: #connection-strings-api}
{: api}

To retrieve user's connection strings from the API, use the [`/users/{userid}/connections`](/apidocs/cloud-databases-api/cloud-databases-api-v5#getconnection) endpoint. You must specify in the path which user and which type of endpoint (public or private) is used in the returned connection strings. The user and endpoint type is not enforced. You can use any user on your deployment with either endpoint (if both exist on your deployment).

```sh
curl -X GET https://api.{region}.databases.cloud.ibm.com/v5/ibm/deployments/{id}/users/{user_type}/{user_id}/connections/{endpoint_type} \
-H 'Authorization: Bearer <>' \
```

## More users and connection strings
{: #connection-strings-add-users}

Access to your {{site.data.keyword.databases-for-redis}} deployment is not limited to the admin user. You can create users by using the _Service Credentials_ page, the {{site.data.keyword.IBM_notm}} Cloud CLI, or through the {{site.data.keyword.IBM_notm}} {{site.data.keyword.databases-for}} API. 

All users on your deployment can use the connection strings, including connection strings for either public or private endpoints. 

### Creating users from the UI
{: #connection-strings-creating-users-service-cred-ui}
{: ui}

1. Navigate to the resource detail page for your service.
2. Click **Service credentials** to open the _Service Credentials_ page.
3. Click **New credential**.
4. Choose a descriptive name for your new credential. 
5. (Optional) Specify whether the new credentials use a public or private endpoint. Use either `{ "service-endpoints": "public" }` / `{ "service-endpoints": "private" }` in the _Add Inline Configuration Parameters_ field to generate connection strings using the specified endpoint. Use of the endpoint is not enforced as it controls which hostnames are in the connection strings. Public endpoints are generated by default.
6. Click **Add** to provision the new credentials. A username and password, and an associated Redis user is auto-generated.

The new credentials appear in the table, and the connection strings are available as JSON in a click-to-copy field under _View Credentials_.

### Creating users from the CLI
{: #connection-strings-creating-users-cli}
{: cli}

If you manage your service through the {{site.data.keyword.cloud_notm}} CLI and the [cloud databases plug-in](/docs/cloud-databases?topic=cloud-databases-icd-cli), you can create a new user with `cdb user-create`. For example, to create a new user for an "example-deployment", use the following command.

```sh
ibmcloud cdb user-create example-deployment <NEWUSERNAME> <NEWPASSWORD>
```
{: .pre}

Once the task has finished, you can retrieve the new user's connection strings with the `ibmcloud cdb deployment-connections` command.

### Creating users with the API
{: #connection-strings-creating-users-api}
{: api}

The _Foundation Endpoint_ that is shown on the _Overview_ panel of your service provides the base URL to access this deployment through the API. To create and manage users, use the base URL with the `/users` endpoint.

```sh
curl -X POST 'https://api.{region}.databases.cloud.ibm.com/v5/ibm/deployments/{id}/users/{user_type}' \
-H "Authorization: Bearer $APIKEY" \
-H "Content-Type: application/json" \
-d '{"user": {"username": "user", "password": "v3ry-1-secUre-pAssword-2"}}'
```
{: .pre}

To retrieve a user's connection strings, use the base URL with the `/users/{userid}/connections` endpoint.

### Adding users to _service credentials_ in the UI
{: #connection-strings-adding-users-service-cred}
{: ui}

Creating a new user from the CLI doesn't automatically populate that user's connection strings into _Service Credentials_. If you want to add them there, you can create a new credential with the existing user information.

Enter the username and password in the JSON field _Add Inline Configuration Parameters_, or specify a file where the JSON information is stored. For example, putting `{"existing_credentials":{"username":"Robert","password":"supersecure"}}` in the field generates _Service Credentials_ with the username "Robert" and password "supersecure" filled into connection strings.

Generating credentials from an existing user does not check for or create that user.
{: tip}

## Connection String Breakdown
{: #connection-strings-breakdown}

### The Redis section
{: #connection-strings-redis-section}

The "Redis" section of a credential created on the *Service credentials* page contains information that is suited for your applications that make connections to Redis.

| Field name | Index | Description |
| ---------- | ----- | ----------- |
| `Type` | | Type of connection - for Redis, it is "URI". |
| `Scheme` | | Scheme for a URI - for Redis, it is "rediss". |
| `Path` | | Path for a URI - for Redis, it is the database number. |
| `Authentication` | `Username`|The username that you use to connect. |
| `Authentication` | `Password`|A password for the user - might be shown as `$PASSWORD`. |
| `Authentication` | `Method`|How authentication takes place; "direct" authentication is handled by the driver. |
| `Hosts` | `0...` | A hostname and port to connect to. |
| `Composed` | `0...` | A URI combining Scheme, authentication, host, and path. |
| `Certificate` | `Name` | The allocated name for the service proprietary certificate for database deployment. |
| `Certificate` | Base64 | A base64 encoded version of the certificate. |
{: caption="Redis/URI connection information" caption-side="top"}

* `0...` indicates that there might be one or more of these entries in an array.

For more information, see [Connecting an external application](/docs/databases-for-redis-gen2?topic=databases-for-redis-external-app).

### The CLI section
{: #connection-strings-cli-section}

The "CLI" section of a credential created on the *Service credentials* page contains information that is suited for command-line clients that make connections to Redis.

| Field name | Index | Description |
| ---------- | ----- | ----------- |
| `Bin` | | The recommended binary to create a connection; in this case it is `redli`. |
| `Composed` | | A formatted command to establish a connection to your deployment. The command combines the `Bin` executable, `Environment` variable settings and uses `Arguments` as command-line parameters. |
| `Environment` | | A list of keys or values you set as environment variables. |
| `Arguments` | 0... | The information that is passed as arguments to the command shown in the Bin field. |
| `Certificate` | Base64| A service proprietary certificate that is used to confirm that an application is connecting to the appropriate server. It is base64 encoded. |
| `Certificate` | Name | The allocated name for the service proprietary certificate. |
| `Type` | | The type of package that uses this connection information; in this case `cli`.  |
{: caption="Redis/cli connection information" caption-side="top"}

* `0...` indicates that there might be one or more of these entries in an array.

For more information, see [Connecting with a command line client](/docs/databases-for-redis-gen2?topic=databases-for-redis-connecting-cli-client).
