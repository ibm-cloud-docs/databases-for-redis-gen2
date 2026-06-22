---
copyright:
  years: 2026
lastupdated: "2026-06-22"

keywords: redis, databases

subcollection: databases-for-redis-gen2

---

{{site.data.keyword.attribute-definition-list}}

# Connecting an external application
{: #external-app}

[Gen 2]{: tag-purple}

Your applications and drivers use connection strings to make a connection to {{site.data.keyword.databases-for-redis_full}}. The service provides connection strings specifically for drivers and applications. Connection strings are displayed in the *Endpoints* panel of your deployment's *Overview* page, and can also be retrieved from the [{{site.data.keyword.databases-for}} CLI plug-in](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-cdb-reference) and the [{{site.data.keyword.databases-for}} API](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-api).

{{site.data.keyword.databases-for-redis}} deployments no longer include a default admin user. Instead, customers create users with 'Manager' or 'Writer' roles using the {{site.data.keyword.cloud}} service credential interface, which is available from the UI or CLI. This process generates credentials for connecting to the deployment. Although these credentials can be used across multiple connections and applications, you are strongly recommended to create dedicated users for each application that are tailored to their specific access requirements. For more information, see [Getting connection strings](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-connection-strings).

## Connection strings for applications
{: #connection-strings-application}

All the information a driver needs to make a connection to your deployment is in the "redis" section of a credential created on the *Service credentials* page. The table contains a breakdown for reference.

| Field name | Index | Description |
| ---------- | ----- | ----------- |
| `Type` | | Type of connection. For Redis, it is "URI". |
| `Scheme` | | Scheme for a URI. For Redis, it is "rediss". |
| `Path` | | Path for a URI. For Redis, it is the database number. |
| `Authentication` | `Username` | The username that you use to connect. |
| `Authentication` | `Password` | A password for the user (might be shown as `$PASSWORD`). |
| `Authentication` | `Method` | How authentication takes place; "direct" authentication is handled by the driver. |
| `Hosts` | `0...` | A hostname and port to connect to. |
| `Composed` | `0...` | A URI combining scheme, authentication, host, and path. |
| `Certificate` | `Name` | The allocated name for the service proprietary certificate for database deployment. |
| `Certificate` | Base64 | A base64 encoded version of the certificate. |
{: caption="redis/URI connection information" caption-side="top"}

* `0...` indicates that there might be one or more of these entries in an array.

Redis drivers are often able to make a connection to your deployment when given the URI-formatted connection string found in the "composed" field of the connection information. For example, if you set the connection string in the environment variable `REDIS_URL`, as in the following example:

```sh
export REDIS_URL=rediss://ibm_cloud_30399dec_4835_4967_a23d_30587a08d9a8:$PASSWORD@e6b2c3f8-54a6-439e-8d8a-aa6c4a78df49.8f7bfd8f3faa4218aec56e069eb46187.databases.appdomain.cloud:32371/0
```
{: pre}

Then, the Node.js client is able to make a connection with:

```javascript
let connectionString = process.env.REDIS_URL;

if (connectionString === undefined) {
  console.error("Please set the REDIS_URL environment variable");
  process.exit(1);
}

let client = null;

client = redis.createClient(connectionString, {
  tls: { servername: new URL(connectionString).hostname }
});
```
{: codeblock}

Alternatively, the connection string can be parsed and its parts sent to the connection handler, as with the following Python client example:

```python
parsed = urlparse(connection_string)

r = redis.StrictRedis(
    host=parsed.hostname,
    port=parsed.port,
    password=parsed.password,
    ssl=True,
    ssl_ca_certs='/etc/ssl/certs/ca-certificates.crt',
    decode_responses=True)
```
{: pre}

Redis has an array of clients for applications to use. A fairly [comprehensive list is maintained on the Redis site](https://redis.io/clients){: external}. Some useful things to keep in mind when choosing a client are features that allow you to easily design your application for the Cloud, like configuring [high-availability](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-redis-ha-dr), security, and service proprietary certificate support.

## Sentinel-aware connections
{: #sentinel-aware-connections}

{{site.data.keyword.databases-for-redis}} Gen 2 uses a Redis Sentinel configuration with 2 Redis instances and 3 Sentinel nodes (2 colocated with Redis instances, 1 isolated) for automatic failover and high availability. For optimal resilience, use client libraries that support Sentinel-aware connections. These clients automatically discover the current primary node and handle failover events seamlessly.

### Recommended Sentinel-aware client libraries
{: #sentinel-client-libraries}

The following client libraries provide robust Sentinel support:

- Node.js: [ioredis](https://github.com/redis/ioredis){: external}. Full Sentinel support with automatic failover handling
- Node.js: [node-redis](https://github.com/redis/node-redis){: external}. Native Sentinel support (v4+)
- Python: [redis-py](https://github.com/redis/redis-py){: external}. Built-in Sentinel client
- Java: [Jedis](https://github.com/redis/jedis){: external} or [Lettuce](https://github.com/lettuce-io/lettuce-core){: external}. Both support Sentinel
- Go: [go-redis](https://github.com/redis/go-redis){: external}. Sentinel support included

### Connecting with Sentinel support (Node.js example)
{: #sentinel-connection-example}

When using a Sentinel-aware client like ioredis, configure your connection to use the Sentinel endpoints:

```javascript
const Redis = require('ioredis');

const client = new Redis({
  sentinels: [
    { host: 'sentinel-host-1', port: 26379 },
    { host: 'sentinel-host-2', port: 26379 },
    { host: 'sentinel-host-3', port: 26379 }
  ],
  name: 'mymaster',
  password: 'your-password',
  sentinelPassword: 'your-sentinel-password',
  tls: {
    rejectUnauthorized: true,
    ca: fs.readFileSync('/path/to/ca-certificate.crt')
  }
});

client.on('error', (err) => {
  console.error('Redis connection error:', err);
});

client.on('ready', () => {
  console.log('Connected to Redis via Sentinel');
});
```
{: codeblock}

The Sentinel-aware client automatically:
- Discovers the current primary node
- Monitors for failover events
- Reconnects to the new primary after failover (typically within 30-90 seconds)
- Handles connection retries and error recovery

For applications requiring maximum availability, implementing connection retry logic and error handling is recommended. For more information, see [Error detection and handling with Redis](https://developer.ibm.com/articles/error-detection-and-handling-with-redis){: external}.
