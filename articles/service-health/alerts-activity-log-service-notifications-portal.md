---
title: 使用 Azure 门户接收有关 Azure 服务通知的活动日志警报
description: 了解如何使用 Azure 门户通过使用 Azure 门户为服务运行状况通知设置活动日志警报。
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 88fbdeeb8bdcc239f1591e053aaf4fb1c36b9b19
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91289780"
---
# <a name="create-activity-log-alerts-on-service-notifications-using-the-azure-portal"></a>使用 Azure 门户创建有关服务通知的活动日志警报
## <a name="overview"></a>概述

本文介绍如何使用 Azure 门户为服务运行状况通知设置活动日志警报。  

服务运行状况通知存储在 [Azure 活动日志](../azure-monitor/platform/platform-logs-overview.md)中。 鉴于活动日志中存储的信息量很大，因此有一个单独的用户界面，以便更轻松地查看和设置有关服务运行状况通知的警报。 

当 Azure 将服务运行状况通知发送到 Azure 订阅时，可以收到警报。 可以基于以下内容配置警报：

- 服务运行状况通知的类别（服务问题、计划内维护、运行状况公告、安全公告）。
- 受影响的订阅。
- 受影响的服务。
- 受影响的区域。

> [!NOTE]
> 服务运行状况通知不发送资源运行状况事件的警报。

还可以配置向其发送警报的人员：

- 选择现有操作组。
- 创建新操作组（可以用于将来的警报）。

若要了解有关操作组的详细信息，请参阅[创建和管理操作组](../azure-monitor/platform/action-groups.md)。

有关如何使用 Azure 资源管理器模板配置服务运行状况通知警报的信息，请参阅[资源管理器模板](../azure-monitor/platform/alerts-activity-log.md)。

### <a name="watch-a-video-on-setting-up-your-first-azure-service-health-alert"></a>观看有关设置第一个 Azure 服务运行状况警报的视频

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OaXt]

## <a name="create-service-health-alert-using-azure-portal"></a>使用 Azure 门户创建服务运行状况警报
1. 在[门户](https://portal.azure.com)中，选择“服务运行状况”。

    ![“服务运行状况”服务](media/alerts-activity-log-service-notifications/home-servicehealth.png)

1. 在“警报”部分中，选择“运行状况警报”。

    ![“运行状况警报”选项卡](media/alerts-activity-log-service-notifications/alerts-blades-sh.png)

1. 选择“添加服务运行状况警报”，并填写字段。

    ![“创建服务运行状况警报”命令](media/alerts-activity-log-service-notifications/service-health-alert.png)

1.   选择要针对其发出警报的“订阅”、“服务”和“区域”。

    [![“添加活动日志警报”对话框](./media/alerts-activity-log-service-notifications/activity-log-alert-new-ux.png)](./media/alerts-activity-log-service-notifications/activity-log-alert-new-ux.png#lightbox)

> [!NOTE]
>此订阅用于保存活动日志警报。 警报资源部署到此订阅，并在其中监视活动日志事件。

5. 选择要针对其发出警报的**事件类型**：   服务问题、计划内维护、运行状况公告和安全公告。

6. 单击“选择操作组”以选择现有操作组或创建新的操作组。 有关操作组的详细信息，请参阅[在 Azure 门户中创建和管理操作组](../azure-monitor/platform/action-groups.md)。


7. 通过输入**警报规则名称**和**说明**定义警报详细信息。

8. 选择要将警报保存到的**资源组**。



在几分钟内，警报将处于活动状态，并根据创建期间指定的条件开始触发。

了解如何[为现有问题管理系统配置 Webhook 通知](service-health-alert-webhook-guide.md)。 有关活动日志警报的 webhook 架构的信息，请参阅 [Azure 活动日志警报的 Webhook](../azure-monitor/platform/activity-log-alerts-webhook.md)。


## <a name="next-steps"></a>后续步骤
- 了解[设置 Azure 服务运行状况警报的最佳做法](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUa)。
- 了解如何[为 Azure 服务运行状况设置移动推送通知](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUw)。
- 了解如何[为现有问题管理系统配置 Webhook 通知](service-health-alert-webhook-guide.md)。
- 了解[服务运行状况通知](service-notifications.md)。
- 了解[通知速率限制](../azure-monitor/platform/alerts-rate-limiting.md)。
- 查看[活动日志警报 webhook 架构](../azure-monitor/platform/activity-log-alerts-webhook.md)。
- 获取[活动日志警报概述](../azure-monitor/platform/alerts-overview.md)，了解如何接收警报。
- 详细了解[操作组](../azure-monitor/platform/action-groups.md)。
