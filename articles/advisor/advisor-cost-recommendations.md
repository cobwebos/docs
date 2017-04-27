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
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 5eef2116f238b477fa8de46ce7b25728c393739c
ms.lasthandoff: 03/31/2017

---

# <a name="advisor-cost-recommendations"></a>顾问成本建议

通过识别闲置和未充分利用的资源，顾问有助于优化和降低 Azure 总支出。 可在顾问仪表板的“成本”选项卡获取成本建议。

![顾问“成本”选项卡](./media/advisor-cost-recommendations/advisor-cost-tab2.png)

## <a name="optimize-virtual-machine-spend-by-resizing-underutilized-instances"></a>通过调整未充分利用的实例来优化虚拟机花费 
虽然某些应用程序方案有意使虚拟机利用率较低，但通过管理虚拟机大小和数量通常可降低成本。 顾问可监视虚拟机 14 天的使用情况，并识别出利用率较低的虚拟机。 如果在 4 天或 4 天以上，虚拟机的 CPU 利用率都小于或等于 5% 且网络使用率小于或等于 7 MB，则将被视为利用率较低的虚拟机。

顾问将显示继续运行虚拟机的预估成本，以便你选择关闭它还是对其进行调整。  

![显示调整虚拟机大小的顾问成本建议](./media/advisor-cost-recommendations/advisor-cost-resizevms.png)

## <a name="use-a-cost-effective-solution-to-manage-performance-goals-of-multiple-sql-databases"></a>使用经济高效的解决方案管理多个 SQL 数据库的性能目标
顾问可识别可从创建弹性数据库池受益的 SQL server 实例。 弹性数据库池是一种简单的低成本高效益的解决方案，用于管理多个数据库的性能目标，其使用模式变化很大。 有关 Azure 弹性数据库池的详细信息，请参阅[什么是 Azure 弹性池？](https://azure.microsoft.com/en-us/documentation/articles/sql-database-elastic-pool/)。

![显示弹性数据库池的顾问成本建议](./media/advisor-cost-recommendations/advisor-cost-elasticdbpools.png)

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>如何访问 Azure 顾问中的成本建议

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 在左窗格中，单击“更多服务”。

3. 在服务菜单窗格中，在“监视和管理”下，单击“Azure 顾问”。  
 将显示顾问仪表板。

4. 在顾问仪表板中，单击“成本”选项卡。

5. 选择要接收建议的订阅，然后单击“获取建议”。

> [!NOTE]
> 若要访问顾问建议，必须先向顾问*注册订阅*。 订阅所有者启动顾问仪表板并单击“获取建议”按钮，即可注册订阅。 这是*一次性操作*。 注册订阅后，可通过订阅、资源组或特定资源的所有者、参与者或读者身份访问顾问建议。

## <a name="next-steps"></a>后续步骤

若要了解有关顾问建议的详细信息，请参阅以下资源：
* [顾问简介](advisor-overview.md)
* [入门](advisor-get-started.md)
* [顾问性能建议](advisor-cost-recommendations.md)
* [顾问高可用性建议](advisor-cost-recommendations.md)
* [顾问安全性建议](advisor-cost-recommendations.md)

