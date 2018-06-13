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
ms.openlocfilehash: e035b49d9e386287baf67bba756f7b58a764acc5
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2018
ms.locfileid: "34173112"
---
若要创建缓存，请先登录到 [Azure 门户](https://portal.azure.com)，并单击“创建资源” > “数据库” > “Redis 缓存”。

![新建缓存](media/redis-cache-create/redis-cache-new-cache-menu.png)

在“新建 Redis 缓存”中，配置新缓存的设置。

| 设置      | 建议的值  | 说明 |
| ------------ |  ------- | -------------------------------------------------- |
| **DNS 名称** | 全局唯一名称 | `-` 字符。 缓存名称的开头或末尾不能是 `-` 字符，并且连续的 `-` 字符无效。  | 
| **订阅** | 订阅 | 要在其下创建此新 Azure Redis 缓存的订阅。 | 
| **资源组** |  TestResources | 要在其中创建缓存的新资源组的名称。 通过将应用的所有资源都放在一个组中，可以一起管理它们。 例如，删除资源组会删除与该应用关联的所有资源。 | 
| **位置** | 美国东部 | 选择将使用缓存的其他服务附近的[区域](https://azure.microsoft.com/regions/)。 |
| **[定价层](https://azure.microsoft.com/pricing/details/cache/)** |  基本 C0（250 MB 缓存） |  定价层决定可用于缓存的大小、性能和功能。 有关详细信息，请参阅 [Azure Redis 缓存概述](../articles/redis-cache/cache-overview.md)。 |
| **固定到仪表板** |  选定 | 单击“将新缓存固定到仪表板”使其容易被找到。 |

![创建缓存](media/redis-cache-create/redis-cache-cache-create.png) 

配置了新缓存设置后，单击“创建”。 

创建缓存可能耗时几分钟。 若要检查状态，可以监视仪表板上的进度。 创建缓存后，新缓存的状态为“正在运行”并且已可供使用。

![创建的缓存](media/redis-cache-create/redis-cache-cache-created.png)

