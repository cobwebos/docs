---
title: "Azure 顾问性能建议 | Microsoft Docs"
description: "使用顾问优化 Azure 部署的性能。"
services: advisor
documentationcenter: NA
author: KumudD
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
ms.openlocfilehash: e32723cd3ef13829890a630f4bff308164e17674
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2017
---
# <a name="advisor-performance-recommendations"></a>顾问性能建议

Azure 顾问性能建议有助于提高关键业务应用程序的速度和响应能力。 可通过顾问从顾问仪表板的“性能”选项卡获取性能建议。

## <a name="improve-database-performance-with-sql-db-advisor"></a>通过 SQL DB 顾问提高数据库性能

顾问针对所有 Azure 资源提供一个一致且统一的建议视图。 它与 SQL 数据库顾问集成，提供建议以改进 SQL Azure 数据库性能。 SQL 数据库顾问通过分析使用情况历史记录来评估 SQL Azure 数据库的性能。 提供的建议最适合运行数据库典型工作负荷。 

> [!NOTE]
> 若要获取建议，数据库必须具有一周左右的使用量，且该周内必须有一些一致的活动。 SQL 数据库顾问优化一致的查询模式比优化随机的突发活动更加轻松。

有关 SQL 数据库顾问的详细信息，请参阅 [SQL 数据库顾问](https://azure.microsoft.com/en-us/documentation/articles/sql-database-advisor/)。

## <a name="improve-redis-cache-performance-and-reliability"></a>提高 Redis 缓存性能和可靠性

Redis 缓存实例的性能可能会受到高内存使用率、服务器负载、网络带宽或大量客户端连接的不利影响，顾问可识别这些实例。 顾问还可提供最佳做法建议，有助于避免潜在问题。 有关 Redis 缓存建议的详细信息，请参阅 [Redis 缓存顾问](https://azure.microsoft.com/en-us/documentation/articles/cache-configure/#redis-cache-advisor)。


## <a name="improve-app-service-performance-and-reliability"></a>提高应用服务性能和可靠性

Azure 顾问集成最佳做法建议，可改善应用服务体验，并发现相关平台的功能。 以下为应用服务建议示例：
* 检测其中应用运行时已耗尽内存或 CPU 资源的实例，并提出缓解措施。
* 检测在其中并置资源（如 Web 应用和数据库）的实例，以改善性能并降低成本。 

有关应用服务建议的详细信息，请参阅 [Best Practices for Azure App Service](https://azure.microsoft.com/en-us/documentation/articles/app-service-best-practices/)（Azure 应用服务的最佳实践）。

## <a name="how-to-access-performance-recommendations-in-advisor"></a>如何访问顾问中的性能建议

1. 登录 [Azure 门户](https://portal.azure.com)，并打开[顾问](https://aka.ms/azureadvisordashboard)。

2.  在顾问仪表板中，单击“性能”选项卡。

## <a name="next-steps"></a>后续步骤

若要了解有关顾问建议的详细信息，请参阅以下资源：

* [顾问简介](advisor-overview.md)
* [顾问入门](advisor-get-started.md)
* [顾问成本建议](advisor-performance-recommendations.md)
* [顾问高可用性建议](advisor-high-availability-recommendations.md)
* [顾问安全性建议](advisor-security-recommendations.md)

