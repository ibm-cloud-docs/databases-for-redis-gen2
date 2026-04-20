---

copyright:
  years: 2026
lastupdated: "2026-04-20"

keywords: redis, databases, connection limits, terminating connections, connection pooling, managing connections

subcollection: databases-for-redis-gen2

---

{{site.data.keyword.attribute-definition-list}}

# Managing connections
{: #managing-redis-connections}

Connections to your {{site.data.keyword.databases-for-redis_full}} deployment use resources, so it is important to consider how many connections you need to tune your deployment's performance. 
{: .shortdesc}

## Redis connection limits 
{: #managing-redis-connection-limits}

At provision, {{site.data.keyword.databases-for-redis_full}} sets the maximum number of connections to your Redis deployment to **10,000**. Leave some connections available, as a number of them are reserved internally to maintain the state and integrity of your database. 

Exceeding the connection limit for your deployment can make your database unreachable by your applications. If your connection limit is reached, you see the following error.

```sh
ERR max number of clients reached
```

### Checking Redis connection limits
{: #checking-redis-connections}

To display your current client connections, use the following CLI command with your [admin user](/docs/databases-for-redis-gen2?topic=databases-for-redis-user-management#the-admin-user).

```sh
CLIENT LIST
```

The output can be filtered.

```sh
CLIENT LIST TYPE NORMAL
```

For more information, see [Redis CLIENT LIST documentation](https://redis.io/commands/client-list/){: .external}.

## Ending Redis connections
{: #managing-redis-connections-end}

Due to the single-threaded nature of Redis, it is not possible to end a client connection while it is running a command. From the client point of view, the connection can never be closed in the middle of the execution of a command. However, the client will notice that the connection is closed only when the next command is sent (and results in network error).

The `CLIENT KILL` Command closes a client connection. 

For more information, see [Redis CLIENT KILL documentation](https://redis.io/commands/client-kill/){: external}.

## Redis connection pooling
{: #managing-redis-connection-pooling}

One way to prevent exceeding the connection limit and ensure that connections from your applications are being handled efficiently is through connection pooling. Connection pooling minimizes the number of active connections against your deployment. For more information, see [The Pooling of Connections in Redis
](https://medium.com/geekculture/the-pooling-of-connections-in-redis-e8188335bf64){: .external}.

## Redis Context-based restrictions and Allowlisting
{: #managing-redis-allowlisting}

You can also use Context-based restrictions or allowlisting to manage and/or limit connections to your Redis deployment. For more information, see [Context-based restrictions](/docs/databases-for-redis-gen2?topic=databases-for-redis-cbr&interface=ui) or [Allowlisting](/docs/databases-for-redis-gen2?topic=databases-for-redis-allowlisting).
