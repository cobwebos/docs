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
ms.openlocfilehash: a662841bebda460d4225e080f16705b3f16fdc46
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-advisor"></a>Azure 顾问入门

了解如何通过 Azure 门户访问顾问，以及如何获取、实现、搜索和刷新建议。

## <a name="get-advisor-recommendations"></a>获取顾问建议

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 在左窗格中，单击“更多服务”。

3. 在服务菜单窗格中，在“监视和管理”下，单击“Azure 顾问”。  
 将显示顾问仪表板。

   ![使用 Azure 门户访问 Azure 顾问](./media/advisor-overview/advisor-azure-portal-menu.png) 

4. 在顾问仪表板上，选择要接收建议的订阅。  
顾问仪表板显示针对所选订阅的个性化建议。 

5. 若要获取特定类别的建议，请单击其中一个选项卡：“高可用性”、“安全”、“性能”或“成本”。
 
> [!NOTE]
> 若要访问顾问建议，必须先向顾问*注册订阅*。 订阅所有者启动顾问仪表板并单击“获取建议”按钮，即可注册订阅。 这是*一次性操作*。 注册订阅后，可通过订阅、资源组或特定资源的所有者、参与者或读者身份访问顾问建议。

  ![Azure 顾问仪表板](./media/advisor-overview/advisor-all-tab.png)

## <a name="get-advisor-recommendation-details-and-implement-a-solution"></a>获取顾问建议详细信息并实现解决方案

顾问中的“建议”边栏选项卡提供有关建议的其他信息。 

1. 登录 [Azure 门户](https://portal.azure.com)，并启动 [Azure 顾问](https://aka.ms/azureadvisordashboard)。

2. 在“顾问建议”仪表板上，单击“获取建议”。

3. 在建议列表中，单击要详细查看的建议。  
“建议”边栏选项卡随即显示。

4. 在“建议”边栏选项卡上，查看可用于解决潜在问题或利用节约成本方法的操作的有关信息。 
  
  ![“顾问建议”边栏选项卡](./media/advisor-overview/advisor-recommendation-action-example.png)

## <a name="search-for-advisor-recommendations"></a>搜索顾问建议

可以搜索特定订阅或资源组的建议。 还可以按状态搜索建议。

1. 登录 [Azure 门户](https://portal.azure.com)，并启动 [Azure 顾问](https://aka.ms/azureadvisordashboard)。

2. 通过筛选订阅、资源组和建议状态（“活跃”或“暂停”）搜索建议。

3. 若要显示基于搜索筛选条件的顾问建议列表，请单击“获取建议”。

  ![顾问搜索筛选条件](./media/advisor-get-started/advisor-search.png)

## <a name="snooze-or-dismiss-advisor-recommendations"></a>推迟或解除顾问建议

1. 登录 [Azure 门户](https://portal.azure.com)，并启动 [Azure 顾问](https://aka.ms/azureadvisordashboard)。

2. 单击“获取建议”，并在建议列表中，单击某个建议。

3. 在“建议”边栏选项卡上，单击“暂停”。  

   ![顾问建议操作示例](./media/advisor-get-started/advisor-snooze.png)

4. 指定推迟时间段，或选择“从不”以解除该建议。


## <a name="next-steps"></a>后续步骤

若要详细了解顾问，请参阅以下资源：
* [Azure 顾问简介](advisor-overview.md)
* [顾问高可用性建议](advisor-high-availability-recommendations.md)
* [顾问安全性建议](advisor-security-recommendations.md)
-  [顾问性能建议](advisor-performance-recommendations.md)
* [顾问成本建议](advisor-performance-recommendations.md)
