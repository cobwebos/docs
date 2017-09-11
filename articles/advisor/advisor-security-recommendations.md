---
title: "Azure 顾问安全性建议 | Microsoft Docs"
description: "使用 Azure 顾问提高 Azure 部署的安全性。"
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
ms.translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 53be05593c3733ccb27979a3a026414013125779
ms.contentlocale: zh-cn
ms.lasthandoff: 03/31/2017

---
# <a name="advisor-security-recommendations"></a>顾问安全性建议

Azure 顾问针对所有 Azure 资源提供一个一致且统一的建议视图。 它与 Azure 安全中心集成，提供安全性建议。 可在顾问仪表板的“安全”选项卡获取安全性建议。

![顾问“安全”按钮](./media/advisor-security-recommendations/advisor-security-tab.png)

安全中心有助于预防、检测和响应威胁，同时增加 Azure 资源的可见性和安全可控性。 可定期分析 Azure 资源的安全状态。 安全中心识别到潜在的安全漏洞时，会创建建议。 此建议将指导你完成配置所需控件的过程。 

![顾问“安全性”选项卡](./media/advisor-security-recommendations/advisor-security-recommendations.png)

有关安全性建议的详细信息，请参阅[管理 Azure 安全中心的安全性建议](https://azure.microsoft.com/en-us/documentation/articles/security-center-recommendations/)。

## <a name="how-to-access-security-recommendations-in-azure-advisor"></a>如何访问 Azure 顾问中的安全性建议

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 在左窗格中，单击“更多服务”。

3. 在服务菜单窗格中，在“监视和管理”下，单击“Azure 顾问”。  
 将显示顾问仪表板。

4. 在顾问仪表板中，单击“安全性”选项卡。

5. 选择要接收建议的订阅，然后单击“获取建议”。

> [!NOTE]
> 若要访问顾问建议，必须先向顾问*注册订阅*。 订阅所有者启动顾问仪表板并单击“获取建议”按钮，即可注册订阅。 这是*一次性操作*。 注册订阅后，可通过订阅、资源组或特定资源的所有者、参与者或读者身份访问顾问建议。

## <a name="next-steps"></a>后续步骤

若要了解有关顾问建议的详细信息，请参阅以下资源：
* [顾问简介](advisor-overview.md)
* [顾问入门](advisor-get-started.md)
* [顾问成本建议](advisor-performance-recommendations.md)
* [顾问性能建议](advisor-performance-recommendations.md)
* [顾问高可用性建议](advisor-high-availability-recommendations.md)


 

