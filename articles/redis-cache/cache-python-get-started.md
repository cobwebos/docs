---
title: "如何将 Azure Redis 缓存与 Python 配合使用 | Microsoft Docs"
description: "开始将 Azure Redis 缓存与 Python 配合使用"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: v-lincan
ms.assetid: f186202c-fdad-4398-af8c-aee91ec96ba3
ms.service: cache
ms.devlang: python
ms.topic: hero-article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 02/10/2017
ms.author: sdanie
ms.translationtype: Human Translation
ms.sourcegitcommit: 5369dcd6ad1ec93c63eb442db9fc5ffdcca37375
ms.openlocfilehash: cdbee52574d0ffbe82ef3dc98f2848f4d00ba2ff
ms.contentlocale: zh-cn
ms.lasthandoff: 02/11/2017

---
# <a name="how-to-use-azure-redis-cache-with-python"></a>如何将 Azure Redis 缓存与 Python 配合使用
> [!div class="op_single_selector"]
> * [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
> * [ASP.NET](cache-web-app-howto.md)
> * [Node.js](cache-nodejs-get-started.md)
> * [Java](cache-java-get-started.md)
> * [Python](cache-python-get-started.md)
> 
> 

本主题说明如何将Azure Redis 缓存与 Python 配合使用。

## <a name="prerequisites"></a>先决条件
安装 [redis-py](https://github.com/andymccurdy/redis-py)。

## <a name="create-a-redis-cache-on-azure"></a>在 Azure 上创建 Redis 缓存
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="retrieve-the-host-name-and-access-keys"></a>检索主机名和访问密钥
[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="enable-the-non-ssl-endpoint"></a>启用非 SSL 终结点
某些 Redis 客户端不支持 SSL，默认情况下， [为新的 Azure Redis 缓存实例禁用了非 SSL 端口](cache-configure.md#access-ports)。 在编写本文时， [redis-py](https://github.com/andymccurdy/redis-py) 客户端不支持 SSL。 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-non-ssl-port.md)]

## <a name="add-something-to-the-cache-and-retrieve-it"></a>在缓存中添加一些内容并检索此内容
    >>> import redis
    >>> r = redis.StrictRedis(host='<name>.redis.cache.windows.net',
          port=6380, db=0, password='<key>', ssl=True)
    >>> r.set('foo', 'bar')
    True
    >>> r.get('foo')
    b'bar'


将 `<name>` 替换为你的缓存名称，将 `key` 替换为你的缓存访问密钥。

<!--Image references-->
[1]: ./media/cache-python-get-started/redis-cache-new-cache-menu.png
[2]: ./media/cache-python-get-started/redis-cache-cache-create.png

