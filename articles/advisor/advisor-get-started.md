---
title: "Azure 顾问入门 | Microsoft Docs"
description: "Azure 顾问入门。"
services: advisor
documentationcenter: NA
author: manbeenkohli
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/10/2017
ms.author: makohli
translationtype: Human Translation
ms.sourcegitcommit: e7fc6cb4ee4752bb7ed90a8f64edcc7c82ec3a88
ms.openlocfilehash: 8ed18f6f62b6d2895e2470df1c3bdd448ea98210
ms.lasthandoff: 02/21/2017

---

# <a name="get-started-with-azure-advisor"></a>Azure 顾问入门

了解如何使用 Azure 门户访问顾问，以及如何获取、实现、搜索和刷新建议。

## <a name="get-advisor-recommendations"></a>获取顾问建议

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 在左侧导航窗格中，单击“更多服务”，然后在服务菜单窗格中，向下滚动到“监视和管理”，然后单击“Azure 顾问”。 这将启动顾问仪表板。

  ![使用 Azure 门户访问 Azure 顾问](./media/advisor-overview/advisor-azure-portal-menu.png) 

3. 在顾问仪表板上，选择要接收建议的订阅。 顾问仪表板显示针对所选订阅的个性化建议。 
4. 若要获取特定类别的建议，请单击其中一个选项卡 -“高可用性”、“安全”、“性能”或“成本”。
 
> [!NOTE]
> 若要访问顾问建议，必须先向顾问**注册**订阅。 **订阅所有者**启动顾问仪表板并单击“获取建议”按钮，即可注册订阅。 这是**一次性操作**。 注册订阅后，**所有者**、**参与者**或**读者**可以访问有关订阅、资源组或特定资源的顾问建议。

  ![Azure 顾问仪表板](./media/advisor-overview/advisor-all-tab.png)

## <a name="get-advisor-recommendation-details-and-implement-a-recommendation"></a>获取顾问建议详细信息并实现建议

顾问中的“建议”边栏选项卡提供有关顾问建议的其他信息。 

1. 登录 [Azure 门户](https://portal.azure.com)，然后启动 [Azure Advisor](https://aka.ms/azureadvisordashboard)。
2. 在“顾问建议”仪表板上，单击“获取建议”。
3. 从建议列表中，单击想要详细查看的建议。 这将启动建议边栏选项卡。
4. 在建议边栏选项卡上，查看可用于解决潜在问题或利用节约成本方法的操作的有关信息。 
  
  ![顾问建议操作示例](./media/advisor-overview/advisor-recommendation-action-example.png)

## <a name="search-for-advisor-recommendations"></a>搜索顾问建议

可以搜索特定订阅或资源组的建议。 还可以按状态搜索建议。

1. 登录 Azure 门户，然后启动 Azure Advisor。
2. 通过筛选订阅、资源组和建议状态（“活跃”或“暂停”）搜索建议。
3. 单击“获取建议”，获取根据搜索筛选得到的顾问建议列表。

  ![](./media/advisor-get-started/advisor-search.png)

## <a name="snooze-advisor-recommendations"></a>暂停顾问建议

1. 登录 Azure 门户，然后启动 Azure Advisor。
2. 单击“获取建议”，然后单击建议列表中的某个建议。
3. 在“建议”边栏选项卡上，单击“暂停”。  可指定暂停时间段或选择“从不”以关闭该建议。

  ![顾问建议操作示例](./media/advisor-get-started/advisor-snooze.png)



## <a name="next-steps"></a>后续步骤

若要了解有关 顾问的详细信息，请参阅以下资源：
-  [Azure 顾问简介](advisor-overview.md)
-  [顾问高可用性建议](advisor-high-availability-recommendations.md)
-  [顾问安全性建议](advisor-security-recommendations.md)
-  [顾问性能建议](advisor-performance-recommendations.md)
-  [顾问成本建议](advisor-performance-recommendations.md)

