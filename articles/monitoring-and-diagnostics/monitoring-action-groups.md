---
title: "在 Azure 门户中创建和管理操作组 | Microsoft 文档"
description: "了解如何在 Azure 门户中创建和管理操作组。"
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: ancav
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 7347be8520e643cd166851d3f525a9a0726b40c8
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>在 Azure 门户中创建和管理器操作组
## <a name="overview"></a>概述 ##
本文演示如何在 Azure 门户中创建和管理操作组。

通过操作组可以配置操作列表。 这些组然后可在定义活动日志警报时使用。 之后，这些组可由你定义的每个活动日志警报重复使用，确保每次触发活动日志警报时采取同样的操作。

操作组可以具有最多 10 种不同的操作类型。 每个操作包含以下属性：

* 名称：操作组中的唯一标识符。  
* **操作类型**：发送短信、发送电子邮件、调用 webhook，或者将数据发送到 ITSM 工具。
* **详细信息**：相应电话号码、电子邮件地址、webhook URI 或 ITSM 连接详细信息。

有关如何使用 Azure 资源管理器模板以配置操作组的信息，请参阅[操作组资源管理器模板](monitoring-create-action-group-with-resource-manager-template.md)。

## <a name="create-an-action-group-by-using-the-azure-portal"></a>使用 Azure 门户创建操作组 ##
1. 在[门户](https://portal.azure.com)中，选择“监视器”。 “监视器”边栏选项卡将所有监视设置和数据合并到一个视图中。

    ![“监视”服务](./media/monitoring-action-groups/home-monitor.png)
2. 在“活动日志”部分，选择“操作组”。

    ![“操作组”选项卡](./media/monitoring-action-groups/action-groups-blade.png)
3. 选择“添加操作组”，并填写字段。

    ![“添加操作组”命令](./media/monitoring-action-groups/add-action-group.png)
4. 在“操作组名称”框中输入名称，然后在“短名称”框中输入名称。 使用此组发送通知时，短名称被用来代替完整的操作组名称。

      ![“添加操作组”对话框](./media/monitoring-action-groups/action-group-define.png)

5. “订阅”框会自动填充当前订阅。 此“订阅”是在其中保存操作组的订阅。

6. 选择在其中保存操作组的“资源组”。

7. 通过为每个操作提供以下项来定义操作列表：

    a. 名称：输入此操作的唯一标识符。

    b. **操作类型**：选择短信、电子邮件、webhook 或 ITSM。

    c. **详细信息**：根据操作类型，输入电话号码、电子邮件地址、webhook URI 或 ITSM 连接详细信息。 对于 ITSM 操作，另外指定 ITSM 工具需要的“工作项”和其他字段。 

> [!NOTE]
> ITSM 操作需要 ITSM 连接。 了解如何创建 [ITSM 连接](../log-analytics/log-analytics-itsmc-overview.md)。 ITSM 操作当前仅适用于活动日志警报。 对于其他警报类型，此操作当前不执行任何操作。
>
>

8. 选择“确定”创建操作组。

## <a name="manage-your-action-groups"></a>管理操作组 ##
创建操作组后，它会在“监视器”边栏选项卡的“操作组”部分显示。 选择要管理的操作组：

* 添加、编辑或删除操作。
* 删除操作组。

## <a name="next-steps"></a>后续步骤 ##
* 详细了解[短信警报行为](monitoring-sms-alert-behavior.md)。  
* 获取[对活动日志警报 webhook 架构的了解](monitoring-activity-log-alerts-webhook.md)。  
* 了解有关 [ITSM 连接器](../log-analytics/log-analytics-itsmc-overview.md)的详细信息
* 详细了解有关警报的[速率限制](monitoring-alerts-rate-limiting.md)。 
* 获取[活动日志警报概述](monitoring-overview-alerts.md)，了解如何接收警报。  
* 了解如何[配置每次发布服务运行状况通知时的警报](monitoring-activity-log-alerts-on-service-notifications.md)。

