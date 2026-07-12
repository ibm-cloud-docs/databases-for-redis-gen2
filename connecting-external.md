---
copyright:
  years: 2026
lastupdated: "2026-07-12"

keywords: redis, databases

subcollection: databases-for-redis-gen2

---

{{site.data.keyword.attribute-definition-list}}

# Connecting an external application
{: #external-app}

[Gen 2]{: tag-purple}

Your applications and drivers use connection strings to make a connection to {{site.data.keyword.databases-for-redis_full}}. The service provides connection strings specifically for drivers and applications. Connection strings are displayed in the *Endpoints* panel of your deployment's *Overview* page, and can also be retrieved from the [{{site.data.keyword.databases-for}} CLI plug-in](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-cdb-reference) and the [{{site.data.keyword.databases-for}} API](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-api).

{{site.data.keyword.databases-for-redis}} deployments no longer include a default admin user. Instead, customers create users with 'Manager' or 'Writer' roles using the {{site.data.keyword.cloud}} service credential interface, which is available from the UI or CLI. This process generates credentials for connecting to the deployment. Although these credentials can be used across multiple connections and applications, you are strongly recommended to create dedicated users for each application that are tailored to their specific access requirements. For more information, see [Getting connection strings](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-connection-strings).

Redis has an array of clients for applications to use. For a comprehensive list of clients, see [Redis clients page](https://redis.io/docs/latest/integrate/){: external}.

When choosing a client, consider features that allow you to easily design your application for the cloud. For example:
- Connection pooling
- Automatic reconnection and failover handling
- TLS/SSL support
- Certificate support
- Client-side caching (for RESP3-compatible clients)

## TLS and certificate support
{: #tls-cert-support}

All connections to {{site.data.keyword.databases-for-redis}} are TLS 1.2 enabled and required. The driver that you use to connect needs to be able to support TLS encryption and the rediss: protocol.

Redis deployments use **Let's Encrypt certificates** for TLS encryption. These certificates are publicly trusted and automatically validated by system CA certificate stores: no custom CA certificate is needed.

### Connection requirements
{: #connection-reqs}

* **Protocol**: rediss:// (TLS-enabled Redis protocol)
* **TLS version**: TLS 1.2 or higher
* **Certificate validation**: Enabled (validates against Let's Encrypt CA)
* **SNI (Server Name Indication)**: Required. Must match the VPE domain


#### Example connection (valkey-cli)
{: #connection-valkey}

```sh
redis-cli -h <vpe-domain> -p 6379 \
  --user <username> -a <password> \
  --tls --sni <vpe-domain>
```
{: pre}


#### Example connection using Node client with iovalkey
{: #connection-iovalkey}

```sh
export REDIS_URL=rediss://$username:$PASSWORD@<hostname>:6379/0
```
{: codeblock}

nodeScript.js
```
#!/usr/bin/env node
const Redis = require('ioredis');
let connectionString = process.env.VALKEY_URL;

if (connectionString === undefined) {
  console.error("Please set the VALKEY_URL environment variable");
  process.exit(1);
}

const client = new Redis(connectionString, {
  tls: {
    servername: new URL(connectionString).hostname
  }
});

client.on('connect', () => {
  console.log('Connected!');
});

client.on('error', (err) => {
  console.error('Redis error:', err);
});
```
{: codeblock}

You might need to install ioredis by running: **npm install ioredis** in your VSI before running the script with **node nodeScript.js**
{: note}


#### Example connection using Python client with redis-py
{: #connection-redis-py}

pythonScript.py
```sh
#!/usr/bin/env python3
import redis
import ssl

r = redis.StrictRedis(
    host='<hostname>',
    port=6379,
    username='<username>',
    password='<password>',
    ssl=True,
    decode_responses=True
)

# Test connection
r.ping()
print("Connected to Valkey")
```
{: codeblock}

You need to install redis-py package by running: **pip3 install redis** in your VSI before running the script with **python3 pythonScript.py**
{: note}


#### Example connection using Go client with go-redis
{: #connection-go-redis}

gosScript.go
```
package main

import (
        "context"
        "crypto/tls"
        "fmt"
        "github.com/redis/go-redis/v9"
)

func main() {
        ctx := context.Background()

        rdb := redis.NewClient(&redis.Options{
                Addr:     "<hostname>:6379",
                Username: "<username>",
                Password: "<password>",
                DB:       0,
                TLSConfig: &tls.Config{
                        MinVersion: tls.VersionTLS12,
                },
        })

        pong, err := rdb.Ping(ctx).Result()
        if err != nil {
                fmt.Println("Connection error:", err)
                return
        }

        fmt.Println("Connected:", pong)
}
```
{: codeblock}

You need to install go-redis packaage by running **go get github.com/redis/go-redis/v9** in your VSI before running the script with **go run goscript.go**
{: note}
