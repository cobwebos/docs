<properties
   pageTitle="如何将 Azure Redis 缓存与 Java 配合使用 | Microsoft Azure"
	description="开始将 Azure Redis 缓存与 Java 配合使用"
	services="redis-cache"
	documentationCenter=""
	authors="steved0x"
	manager="douge"
	editor=""/>

<tags
	ms.service="cache"
	ms.devlang="java"
	ms.topic="hero-article"
	ms.tgt_pltfrm="cache-redis"
	ms.workload="tbd"
	ms.date="08/24/2016"
	ms.author="sdanie"/>

# 如何将 Azure Redis 缓存与 Java 配合使用

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Azure Redis 缓存可让你访问 Microsoft 管理的专用 Redis 缓存。可从 Microsoft Azure 内部的任何应用程序访问你的缓存。

本主题说明如何将Azure Redis 缓存与 Java 配合使用。

## 先决条件

[Jedis](https://github.com/xetorthio/jedis) - Redis 的 Java 客户端

本教程使用 Jedis，但你可以使用 [http://redis.io/clients](http://redis.io/clients) 中列出的任何 Java 客户端。

## 在 Azure 上创建 Redis 缓存

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## 检索主机名和访问密钥

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## 启用非 SSL 终结点

某些 Redis 客户端不支持 SSL，默认情况下，[为新的 Azure Redis 缓存实例禁用了非 SSL 端口](cache-configure.md#access-ports)。在编写本文时，[Jedis](https://github.com/xetorthio/jedis) 客户端不支持 SSL。

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-non-ssl-port.md)]




## 在缓存中添加一些内容并检索此内容

	package com.mycompany.app;
	import redis.clients.jedis.Jedis;
	import redis.clients.jedis.JedisShardInfo;

	/* Make sure you turn on non-SSL port in Azure Redis using the Configuration section in the Azure Portal */
	public class App
	{
	  public static void main( String[] args )
	  {
        /* In this line, replace <name> with your cache name: */
	    JedisShardInfo shardInfo = new JedisShardInfo("<name>.redis.cache.windows.net", 6379);
	    shardInfo.setPassword("<key>"); /* Use your access key. */
	    Jedis jedis = new Jedis(shardInfo);
     	jedis.set("foo", "bar");
     	String value = jedis.get("foo");
	  }
	}


## 后续步骤

- [启用缓存诊断](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics)，以便可以[监视](https://msdn.microsoft.com/library/azure/dn763945.aspx)缓存的运行状况。
- 阅读官方 [Redis 文档](http://redis.io/documentation)。

<!---HONumber=AcomDC_0921_2016-->