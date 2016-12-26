---
title: "Azure 顾问成本建议 | Microsoft Docs"
description: "使用 Azure 顾问优化 Azure 部署的成本。"
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
ms.sourcegitcommit: a560aa9cc6c70fd3827f649627466c9071b8ab8b
ms.openlocfilehash: 39a882a8338ce820c6b9b98f58d1a9db7448c7be

---

# <a name="advisor-cost-recommendations"></a>顾问成本建议

通过识别闲置和未充分利用的资源，顾问有助于优化和降低 Azure 总支出。 可在顾问仪表板的“成本”选项卡获取成本建议。

![顾问“成本”选项卡](./media/advisor-cost-recommendations/advisor-cost-tab2.png)

## <a name="low-utilization-virtual-machines"></a>利用率较低的虚拟机 

虽然某些应用程序方案有意使虚拟机利用率较低，但通过管理虚拟机大小和数量通常可降低成本。 顾问可监视虚拟机 14 天的使用情况，并识别出利用率较低的虚拟机。 如果在 4 天或 4 天以上，虚拟机的 CPU 利用率都小于或等于 5% 且网络使用率小于或等于 7 MB，则将被视为利用率较低的虚拟机。

顾问会显示继续运行该虚拟机的预计成本。 可选择关闭虚拟机或调整其大小。  

![显示调整虚拟机大小的顾问成本建议](./media/advisor-cost-recommendations/advisor-cost-resizevms.png)

## <a name="sql-elastic-database-pool-recommendations"></a>SQL 弹性数据库池建议

顾问可识别可从创建弹性数据库池受益的 SQL server 实例。 弹性数据库池是一种简单的低成本高效益的解决方案，用于管理多个数据库的性能目标，其使用模式变化很大。 有关 Azure 弹性数据库池的详细信息，请参阅[什么是 Azure 弹性池？](https://azure.microsoft.com/en-us/documentation/articles/sql-database-elastic-pool/)

![显示弹性数据库池的顾问成本建议](./media/advisor-cost-recommendations/advisor-cost-elasticdbpools.png)

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>如何访问 Azure 顾问中的成本建议

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 在左侧导航窗格中，单击“更多服务”，在服务菜单窗格中，向下滚动到“监视和管理”，然后单击“Azure Advisor”。 这将启动顾问仪表板。 
3. 在顾问仪表板上，单击“成本”选项卡，选择要接收建议的订阅，然后单击“获取建议”

> [!NOTE]
> 如果订阅中已用户分配了**所有者**、**参与者**或**读者**角色，则 Azure 顾问会为其生成建议。

## <a name="next-steps"></a>后续步骤

若要了解有关顾问建议的详细信息，请参阅以下资源：
-  [顾问简介](advisor-overview.md)
-  [入门](advisor-get-started.md)
-  [顾问高可用性建议](advisor-cost-recommendations.md)
-  [顾问安全性建议](advisor-cost-recommendations.md)
-  [顾问性能建议](advisor-cost-recommendations.md)



<!--HONumber=Nov16_HO3-->


