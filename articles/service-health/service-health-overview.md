---
title: "Azure 服务运行状况概述 | Microsoft Docs"
description: "有关当前和将来的 Azure 服务问题及维护对 Azure 应用造成的影响的个性化信息。"
services: Resource health
documentationcenter: 
author: rboucher
manager: 
editor: 
ms.assetid: 
ms.service: service-health
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Supportability
ms.date: 07/07/2017
ms.author: robb
ms.openlocfilehash: 001dc1fa2a0fd7e132101944a87be3f8552d8738
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="azure-service-health"></a>Azure 服务运行状况
当 Azure 服务中的问题影响到个人的服务时，Azure 服务运行状况会提供及时和个性化的信息。  借助此功能还可对即将进行的计划内维护做好准备。

## <a name="service-health-events"></a>服务运行状况事件
服务运行状况跟踪三种可能会影响资源的运行状况事件：
1. **服务问题** - Azure 服务中目前已经造成了影响的问题。 
2. **计划内维护** - 即将进行的且将来可能会影响服务可用性的维护。  
3. **运行状况通告** - Azure 服务中发生的、需要引以注意的更改。 示例包括已弃用某些 Azure 功能，或超出了使用配额。

    ![服务运行状况事件](./media/service-health-overview/azure-service-health-overview-7.png)

## <a name="get-started-with-service-health"></a>服务运行状况入门
若要启动服务运行状况仪表板，请在门户仪表板上选择“服务运行状况”磁贴。 如果以前删除了该磁贴或者正在使用自定义仪表板，请在“更多服务”（仪表板左下角）中搜索“服务运行状况”服务。
![服务运行状况入门](./media/service-health-overview/azure-service-health-overview-1.png)

## <a name="see-current-issues-which-impact-your-services"></a>查看当前影响了服务的问题
“服务问题”视图显示 Azure 服务中当前正在影响资源的所有问题。 可以了解问题的开始时间，以及受影响的服务和区域。 还可以阅读最新的更新，了解 Azure 正在采取哪些措施来解决问题。 
![管理服务问题](./media/service-health-overview/azure-service-health-overview-2.png)

选择“潜在影响”选项卡查看自己拥有的、可能被问题影响的特定资源列表。 可以下载这些资源的 CSV 列表，以便与团队共享。
![管理服务问题 - 影响](./media/service-health-overview/azure-service-health-overview-4.png)

## <a name="get-links-and-downloadable-explanations"></a>获取链接和可下载的说明 
可以获取要在问题管理系统中使用的问题链接。 可以下载 PDF 文件（有时是 CSV 文件），以便与无法访问 Azure 门户的人员共享。   
![管理服务问题 - 问题管理](./media/service-health-overview/azure-service-health-overview-3.png)

## <a name="get-support-from-microsoft"></a>获取 Microsoft 支持
如果即使在解决问题之后，资源仍处于错误状态，请联系支持人员。  请使用页面右侧的支持链接。  

## <a name="pin-a-personalized-health-map-to-your-dashboard"></a>将个性化运行状况图固定到仪表板
筛选服务运行状况，显示业务关键型订阅、区域和资源类型。 保存筛选结果，将个性化运行状况全局图固定到门户仪表板。 
![筛选个性化运行状况图](./media/service-health-overview/azure-service-health-overview-6a.png)
![固定个性化运行状况图](./media/service-health-overview/azure-service-health-overview-6b.png)

## <a name="configure-service-health-alerts"></a>配置服务运行状况警报
Azure 服务运行状况与 Azure Monitor 集成，当业务关键型资源受到影响时，它可以通过电子邮件、短信和 Webhook 通知发出警报。 针对相应的服务运行状况事件设置活动日志警报。 使用“操作组”将该警报路由到组织中的相应人员。 有关详细信息，请参阅[配置服务运行状况的警报](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md)

# <a name="next-steps"></a>后续步骤
设置警报，以便收到运行状况问题的通知。 有关详细信息，请参阅[配置服务运行状况的警报](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md)。 