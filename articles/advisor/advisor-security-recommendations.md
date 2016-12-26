---
title: "Azure 顾问安全性建议 | Microsoft Docs"
description: "使用 Azure 顾问提高 Azure 部署的安全性。"
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
ms.openlocfilehash: 4345e544d2e2bcc90ee2e3caf5bf6dfc19f01ca3

---
# <a name="advisor-security-recommendations"></a>顾问安全性建议

顾问针对所有 Azure 资源提供一个一致且统一的建议视图。 它与 Azure 安全中心集成，提供安全性建议。 可在顾问仪表板的“安全”选项卡获取安全性建议。

![顾问安全性选项卡](./media/advisor-security-recommendations/advisor-security-tab.png)

安全中心有助于预防、检测和响应威胁，同时增加 Azure 资源的可见性和安全可控性。 可定期分析 Azure 资源的安全状态。 安全中心识别到潜在的安全漏洞时，会创建建议。 此建议将指导你完成配置所需控件的过程。 

![顾问安全性选项卡](./media/advisor-security-recommendations/advisor-security-recommendations.png)

有关安全性建议的详细信息，请参阅[管理 Azure 安全中心的安全性建议](https://azure.microsoft.com/en-us/documentation/articles/security-center-recommendations/)

## <a name="how-to-access-security-recommendations-in-azure-advisor"></a>如何访问 Azure 顾问中的安全性建议

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 在左侧导航窗格中，单击“更多服务”，在服务菜单窗格中，向下滚动到“监视和管理”，然后单击“Azure Advisor”。 这将启动顾问仪表板。 
3. 在顾问仪表板上，单击“安全”选项卡，然后选择要接收建议的订阅。

> [!NOTE]
> 如果订阅中已为用户分配了**所有者、参与者或读者**角色，则顾问会为其生成建议。  

## <a name="next-steps"></a>后续步骤

若要了解有关顾问建议的详细信息，请参阅以下资源：
-  [顾问高可用性建议](advisor-high-availability-recommendations.md)
-  [顾问性能建议](advisor-performance-recommendations.md)
-  [顾问成本建议](advisor-performance-recommendations.md)
 



<!--HONumber=Nov16_HO3-->


