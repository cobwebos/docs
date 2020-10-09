---
title: include 文件
description: include 文件
services: redis-cache
author: curib
ms.service: cache
ms.topic: include
ms.date: 10/06/2020
ms.author: cauribeg
ms.custom: include file
ms.openlocfilehash: ae9526a47a77118503274fc824538980ebcd50de
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2020
ms.locfileid: "91828922"
---
1. 若要创建缓存，请登录到 [Azure 门户](https://portal.azure.com)并选择“创建资源”  。

    :::image type="content" source="media/redis-cache-create/create-resource.png" alt-text="选择 Azure Cache for Redis。":::

   
1. 在“新建”页上选择“数据库”，然后选择“Azure Cache for Redis”。

    :::image type="content" source="media/redis-cache-create/select-cache.png" alt-text="选择 Azure Cache for Redis。":::
   
1. 在“新建 Redis 缓存”页上配置新缓存的设置。
   
   | 设置      | 建议的值  | 说明 |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS 名称** | 输入任何全局唯一的名称。 | 缓存名称必须是包含 1 到 63 个字符的字符串，只能包含数字、字母或连字符。 该名称必须以数字或字母开头和结尾，且不能包含连续的连字符。 缓存实例的主机名是 *\<DNS name> .redis.cache.windows.net*。 | 
   | **订阅** | 单击下拉箭头并选择你的订阅。 | 要在其下创建此新的 Azure Cache for Redis 实例的订阅。 | 
   | **资源组** | 单击下拉箭头并选择一个资源组，或者选择“新建”并输入新的资源组名称。 | 要在其中创建缓存和其他资源的资源组的名称。 将所有应用资源放入一个资源组可以轻松地统一管理或删除这些资源。 | 
   | **位置** | 单击下拉箭头并选择一个位置。 | 选择与要使用该缓存的其他服务靠近的[区域](https://azure.microsoft.com/regions/)。 |
   | **定价层** | 单击下拉箭头并选择一个[定价层](https://azure.microsoft.com/pricing/details/cache/)。 |  定价层决定可用于缓存的大小、性能和功能。 有关详细信息，请参阅[用于 Redis 的 Azure 缓存概述](../articles/azure-cache-for-redis/cache-overview.md)。 |

1. 选择“网络”选项卡，或单击页面底部的“网络”按钮 。

1. 在“网络”选项卡中，选择你的连接方法。

1. 选择页面底部的“下一步:高级”选项卡，或者单击页面底部的“下一步:高级”按钮。

1. 在基本或标准缓存实例的“高级”选项卡中，如果想要启用非 TLS 端口，请选择启用开关。

1. 在高级缓存实例的“高级”选项卡中，配置非 TLS 端口、群集和数据持久性的设置。

1. 选择页面底部的“下一步:标记”选项卡，或者单击“下一步:标记”按钮。

1. 或者，在“标记”选项卡中，如果希望对资源分类，请输入名称或值。 

1. 选择“查看 + 创建”。 **** 随后你会转到“查看 + 创建”选项卡，Azure 将在此处验证配置。

1. 显示绿色的“已通过验证”消息后，选择“创建”。

创建缓存需要花费片刻时间。 可在 Azure Cache for Redis 的“概述”页面上监视进度 ****  。 如果“状态”显示为“正在运行”，则表示该缓存可供使用。 ****   **** 