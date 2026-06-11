---

copyright:
  years: 2026
lastupdated: "2026-06-11"

keywords: redis, databases, connection limits, terminating connections, connection pooling, managing connections

subcollection: databases-for-redis-gen2

---

{{site.data.keyword.attribute-definition-list}}

# Managing connections
{: #managing-redis-connections}

[Gen 2]{: tag-purple}

Connections to your {{site.data.keyword.databases-for-redis_full}} deployment use resources, so it is important to consider how many connections you need to tune your deployment's performance.
{: .shortdesc}

## Redis connection limits
{: #managing-redis-connection-limits}

At provision, {{site.data.keyword.databases-for-redis_full}} sets the maximum number of connections to your Redis deployment to **10,000**. Leave some connections available, as a number of them are reserved internally to maintain the state and integrity of your database.

Exceeding the connection limit for your deployment can make your database unreachable by your applications. If your connection limit is reached, you see the following error.

```sh
ERR max number of clients reached
```
{: pre}

### Checking Redis connection limits
{: #checking-redis-connections}

To display your current client connections, use the following CLI command with your user credentials.

```sh
CLIENT LIST
```
{: pre}

The output can be filtered:

```sh
CLIENT LIST TYPE NORMAL
```
{: pre}

For more information, see [Redis CLIENT LIST documentation](https://redis.io/commands/client-list/){: .external}.

## Ending Redis connections
{: #managing-redis-connections-end}

Because of Redis's single-threaded command execution model, a client connection cannot be terminated while a command is still running. The server processes the disconnect only after the current command completes. As a result, the client typically becomes aware of the closed connection only when it sends the next command and receives a network error.

The `CLIENT KILL` command closes a client connection but with a limitation that it processes the kill request only after the running command finishes. For more information, see [Redis CLIENT KILL documentation](https://redis.io/commands/client-kill/){: external}.

The real solution is avoid commands that block the server for a long time, such as `KEYS *`, expensive Lua scripts, huge `SORT` commands, and large blocking module operations. Instead you should use incremental and non-blocking alternatives.

If the server is completely stuck on an expensive command, the only immediate way to interrupt is to terminate the Redis process itself or restart the service. However, this is disruptive and can affect all clients.

## Redis connection pooling
{: #managing-redis-connection-pooling}

One way to prevent exceeding the connection limit and ensure that connections from your applications are being handled efficiently is through connection pooling. Connection pooling minimizes the number of active connections against your deployment. For more information, see [The Pooling of Connections in Redis](https://medium.com/geekculture/the-pooling-of-connections-in-redis-e8188335bf64){: .external}.

## Redis context-based restrictions and allowlisting
{: #managing-redis-allowlisting}

You can also use context-based restrictions or allowlisting to manage and limit connections to your Redis deployment. For more information, see [Context-based restrictions](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-cbr&interface=ui) and [Allowlisting](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-allowlisting).
