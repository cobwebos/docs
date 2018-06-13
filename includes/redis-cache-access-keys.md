---
title: include 文件
description: include 文件
services: redis-cache
author: wesmc7777
ms.service: cache
ms.topic: include
ms.date: 03/28/2018
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: d1ae8e5dfbb1455d639e3e2119a4606a8c3a0047
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
ms.locfileid: "32196515"
---
### <a name="retrieve-host-name-ports-and-access-keys-using-the-azure-portal"></a>使用 Azure 门户检索主机名、端口和访问密钥

连接到某个 Azure Redis 缓存实例时，缓存客户端需要该缓存的主机名、端口和密钥。 在某些客户端中，这些项的名称可能略有不同。 可以在 Azure 门户中检索此信息。

若要使用 [Azure 门户](https://portal.azure.com)检索访问密钥，请浏览到缓存，然后单击“访问密钥”。 

![Redis 缓存密钥](media/redis-cache-access-keys/redis-cache-keys.png)

若要检索主机名、端口，请单击“属性”。

![Redis 缓存属性](media/redis-cache-access-keys/redis-cache-hostname-ports.png)

