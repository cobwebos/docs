---
title: "如何将 Azure Redis 缓存与 Java 配合使用 | Microsoft Docs"
description: "开始将 Azure Redis 缓存与 Java 配合使用"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 29275a5e-2e39-4ef2-804f-7ecc5161eab9
ms.service: cache
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 08/24/2016
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: 408026a8e75272cea92ad62e3a75aabaadf98351
ms.openlocfilehash: 00c4d2e31391297955ecba891e919bda65ddaf8e


---
# <a name="how-to-use-azure-redis-cache-with-java"></a>如何将 Azure Redis 缓存与 Java 配合使用
> [!div class="op_single_selector"]
> * [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
> * [ASP.NET](cache-web-app-howto.md)
> * [Node.js](cache-nodejs-get-started.md)
> * [Java](cache-java-get-started.md)
> * [Python](cache-python-get-started.md)
> 
> 

Azure Redis 缓存可让你访问 Microsoft 管理的专用 Redis 缓存。 可从 Microsoft Azure 内部的任何应用程序访问你的缓存。

本主题说明如何将Azure Redis 缓存与 Java 配合使用。

## <a name="prerequisites"></a>先决条件
[Jedis](https://github.com/xetorthio/jedis) - Redis 的 Java 客户端

本教程使用 Jedis，但你可以使用 [http://redis.io/clients](http://redis.io/clients)中列出的任何 Java 客户端。

## <a name="create-a-redis-cache-on-azure"></a>在 Azure 上创建 Redis 缓存
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="retrieve-the-host-name-and-access-keys"></a>检索主机名和访问密钥
[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="enable-the-non-ssl-endpoint"></a>启用非 SSL 终结点
某些 Redis 客户端不支持 SSL，默认情况下， [为新的 Azure Redis 缓存实例禁用了非 SSL 端口](cache-configure.md#access-ports)。 在编写本文时， [Jedis](https://github.com/xetorthio/jedis) 客户端不支持 SSL。 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-non-ssl-port.md)]

## <a name="add-something-to-the-cache-and-retrieve-it"></a>在缓存中添加一些内容并检索此内容
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


## <a name="next-steps"></a>后续步骤
* [启用缓存诊断](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics)，以便可以[监视](https://msdn.microsoft.com/library/azure/dn763945.aspx)缓存的运行状况。
* 阅读官方 [Redis 文档](http://redis.io/documentation)。




<!--HONumber=Nov16_HO5-->


