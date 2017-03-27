---
title: "Azure 顾问性能建议 | Microsoft Docs"
description: "使用顾问优化 Azure 部署的性能。"
services: advisor
documentationcenter: NA
author: kumudd
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
translationtype: Human Translation
ms.sourcegitcommit: e7fc6cb4ee4752bb7ed90a8f64edcc7c82ec3a88
ms.openlocfilehash: d04c209894ab8ee35b92381f9679f68addd64f00
ms.lasthandoff: 02/21/2017

---

# <a name="advisor-performance-recommendations"></a>顾问性能建议

顾问性能建议有助于提高和改善关键业务应用程序的速度和响应能力。 可使用顾问从顾问仪表板的“**性能**”选项卡获取性能建议。

![“顾问性能”选项卡](./media/advisor-performance-recommendations/advisor-performance-tab.png)

## <a name="sql-database-recommendations"></a>SQL 数据库建议

顾问针对所有 Azure 资源提供一个一致且统一的建议视图。 它与 SQL 数据库顾问集成，提供建议以改进 SQL Azure 数据库性能。 SQL 数据库顾问通过分析使用情况历史记录来评估 SQL Azure 数据库的性能。 提供的建议最适合运行数据库典型工作负荷。 

> [!NOTE]
> 若要获取建议，数据库必须具有一周左右的使用量，且该周内必须有一些一致的活动。 SQL 数据库顾问优化一致的查询模式比优化随机的突发活动更加轻松。

有关 SQL 数据库顾问的详细信息，请参阅 [SQL 数据库顾问](https://azure.microsoft.com/en-us/documentation/articles/sql-database-advisor/)。

![SQL 数据库建议](./media/advisor-performance-recommendations/advisor-performance-sql.png)

## <a name="redis-cache-recommendations"></a>Redis 缓存建议

Redis 缓存实例的性能可能会受到高内存使用率、服务器负载、网络带宽或大量客户端连接的不利影响，Azure 顾问可识别这些实例。 它还可提供最佳做法建议，以避免潜在问题。 有关 Redis 缓存建议的详细信息，请参阅 [Redis 缓存顾问](https://azure.microsoft.com/en-us/documentation/articles/cache-configure/#redis-cache-advisor)。


## <a name="app-services-recommendations"></a>应用服务建议

Azure 顾问集成最佳做法建议，可改善应用服务体验，并发现相关平台的功能。 以下为应用服务建议示例：
- 检测其中应用运行时已耗尽内存或 CPU 资源的实例，并提出缓解措施
- 检测在其中并置资源（如 Web 应用和数据库）的实例，以改善性能并降低成本。 

有关应用服务建议的详细信息，请参阅 [Best Practices for Azure App Service](https://azure.microsoft.com/en-us/documentation/articles/app-service-best-practices/)（Azure 应用服务的最佳实践）。
![应用服务建议](./media/advisor-performance-recommendations/advisor-performance-app-service.png)

## <a name="how-to-access-performance-recommendations-in-advisor"></a>如何访问顾问中的性能建议

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 在左侧导航窗格中，单击“更多服务”，在服务菜单窗格中，向下滚动到“监视和管理”，然后单击“Azure Advisor”。 这将启动顾问仪表板。 
3. 在顾问仪表板上，单击“性能”选项卡，然后选择要接收建议的订阅。

> [!NOTE]
> 若要访问顾问建议，必须先向顾问**注册**订阅。 **订阅所有者**启动顾问仪表板并单击“获取建议”按钮，即可注册订阅。 这是**一次性操作**。 注册订阅后，**所有者**、**参与者**或**读者**可以访问有关订阅、资源组或特定资源的顾问建议。

## <a name="next-steps"></a>后续步骤

若要了解有关顾问建议的详细信息，请参阅以下资源：

-  [顾问简介](advisor-overview.md)
-  [顾问入门](advisor-get-started.md)
-  [顾问高可用性建议](advisor-high-availability-recommendations.md)
-  [顾问安全性建议](advisor-security-recommendations.md)
-  [顾问成本建议](advisor-performance-recommendations.md)

