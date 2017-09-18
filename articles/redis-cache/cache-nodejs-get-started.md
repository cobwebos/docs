---
title: "如何将 Azure Redis 缓存与 Node.js 配合使用 | Microsoft Docs"
description: "开始将 Azure Redis 缓存与 Node.js 和 node_redis 配合使用。"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: v-lincan
ms.assetid: 06fddc95-8029-4a8d-83f5-ebd5016891d9
ms.service: cache
ms.devlang: nodejs
ms.topic: hero-article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 02/10/2017
ms.author: sdanie
ms.translationtype: HT
ms.sourcegitcommit: 12c20264b14a477643a4bbc1469a8d1c0941c6e6
ms.openlocfilehash: f2c448af24e180db58f3ef3d39e90036dda3f7eb
ms.contentlocale: zh-cn
ms.lasthandoff: 09/07/2017

---
# <a name="how-to-use-azure-redis-cache-with-nodejs"></a>如何将 Azure Redis 缓存与 Node.js 配合使用
> [!div class="op_single_selector"]
> * [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
> * [ASP.NET](cache-web-app-howto.md)
> * [Node.js](cache-nodejs-get-started.md)
> * [Java](cache-java-get-started.md)
> * [Python](cache-python-get-started.md)
> 
> 

Azure Redis 缓存用于访问 Microsoft 管理的安全专用的 Redis 缓存。 可从 Microsoft Azure 内部的任何应用程序访问缓存。

本主题说明如何将Azure Redis 缓存与 Node.js 配合使用。 

## <a name="prerequisites"></a>先决条件
安装 [node_redis](https://github.com/mranney/node_redis)：

    npm install redis

本教程使用的是 [node_redis](https://github.com/mranney/node_redis)。 有关使用其他 Node.js 客户端的示例，请参阅 [Node.js Redis 客户端](http://redis.io/clients#nodejs)中所列的适用于 Node.js 客户端的各个文档。

## <a name="create-a-redis-cache-on-azure"></a>在 Azure 上创建 Redis 缓存
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="retrieve-the-host-name-and-access-keys"></a>检索主机名和访问密钥
[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="connect-to-the-cache-securely-using-ssl"></a>使用 SSL 安全连接到缓存
最新版本的 [node_redis](https://github.com/mranney/node_redis) 支持使用 SSL 连接到 Azure Redis 缓存。 下面的示例展示了如何使用 SSL 终结点 6380 连接到 Azure Redis 缓存。 将 `<name>` 替换为缓存名称，将 `<key>` 替换为主密钥或辅助密钥，如前面的[检索主机名和访问密钥](#retrieve-the-host-name-and-access-keys)部分中所述。

     var redis = require("redis");

      // Add your cache name and access key.
    var client = redis.createClient(6380,'<name>.redis.cache.windows.net', {auth_pass: '<key>', tls: {servername: '<name>.redis.cache.windows.net'}});

> [!NOTE]
> 已为新的 Azure Redis 缓存实例禁用了非 SSL 端口。 如果使用的是其他不支持 SSL 的客户端，请参阅[如何启用非 SSL 端口](cache-configure.md#access-ports)。
> 
> 

## <a name="add-something-to-the-cache-and-retrieve-it"></a>在缓存中添加一些内容并检索此内容
下面的示例展示了如何连接到 Azure Redis 缓存实例，以及如何在缓存中存储和检索项目。 有关将 Redis 与 [node_redis](https://github.com/mranney/node_redis) 客户端一起使用的更多示例，请参阅 [http://redis.js.org/](http://redis.js.org/)。

     var redis = require("redis");

      // Add your cache name and access key.
    var client = redis.createClient(6380,'<name>.redis.cache.windows.net', {auth_pass: '<key>', tls: {servername: '<name>.redis.cache.windows.net'}});

    client.set("key1", "value", function(err, reply) {
            console.log(reply);
        });

    client.get("key1",  function(err, reply) {
            console.log(reply);
        });

输出：

    OK
    value


## <a name="next-steps"></a>后续步骤
* [启用缓存诊断](cache-how-to-monitor.md#enable-cache-diagnostics)，以便可以[监视](cache-how-to-monitor.md)缓存的运行状况。
* 阅读官方 [Redis 文档](http://redis.io/documentation)。


