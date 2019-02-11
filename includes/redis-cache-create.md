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
ms.openlocfilehash: 0f1decae5fb3ec4a07f01c5bff7475f3d73a3cbb
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2018
ms.locfileid: "53111955"
---
1. 若要创建缓存，请首先登录到 [Azure 门户](https://portal.azure.com)。 然后选择“创建资源” > “数据库” > “用于 Redis 的 Azure 缓存”。

    ![新建缓存](media/redis-cache-create/redis-cache-new-cache-menu.png)

2. 在“新建用于 Redis 的 Azure 缓存”中，配置新缓存的设置。

    | 设置      | 建议的值  | Description |
    | ------------ |  ------- | -------------------------------------------------- |
    | **DNS 名称** | 全局唯一名称 | 缓存名称。 必须是 1 到 63 个字符的字符串，只能包含数字、字母和 `-` 字符。 缓存名称的开头或末尾不能是 `-` 字符，并且连续的 `-` 字符无效。  | 
    | **订阅** | 订阅 | 要在其下创建此新的用于 Redis 的 Azure 缓存实例的订阅。 | 
    | **资源组** |  TestResources | 要在其中创建缓存的新资源组的名称。 通过将应用的所有资源都放在一个组中，可以一起管理它们。 例如，删除资源组会删除与该应用关联的所有资源。 | 
    | **位置** | 美国东部 | 选择将使用缓存的其他服务附近的[区域](https://azure.microsoft.com/regions/)。 |
    | **[定价层](https://azure.microsoft.com/pricing/details/cache/)** |  基本 C0（250 MB 缓存） |  定价层决定可用于缓存的大小、性能和功能。 有关详细信息，请参阅[用于 Redis 的 Azure 缓存概述](../articles/azure-cache-for-redis/cache-overview.md)。 |
    | **固定到仪表板** |  选定 | 将新缓存固定到仪表板，使其容易被找到。 |

    ![创建缓存](media/redis-cache-create/redis-cache-cache-create.png) 

3. 配置了新缓存设置后，选择“创建”。 

    创建缓存可能耗时几分钟。 若要检查状态，可以监视仪表板上的进度。 缓存在创建后会显示状态为“正在运行”，可供用户使用。

    ![创建的缓存](media/redis-cache-create/redis-cache-cache-created.png)

