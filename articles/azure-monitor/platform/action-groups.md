---
title: 在 Azure 门户中创建和管理器操作组
description: 了解如何在 Azure 门户中创建和管理操作组。
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 38cccf17980f5a6a2cf162cdecdc6aad40d4f38e
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2019
ms.locfileid: "54432551"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>在 Azure 门户中创建和管理器操作组
## <a name="overview"></a>概述 ##
操作组是由 Azure 订阅的所有者定义的通知首选项的集合。 Azure Monitor 和服务运行状况警报使用操作组来通知用户某个警报已触发。 各种警报可以使用相同的操作组或不同的操作组，具体取决于用户的要求。

当操作配置为通过电子邮件或短信来通知某个人员时，该人员将收到确认，指出他 / 她已被添加到操作组。

本文演示如何在 Azure 门户中创建和管理操作组。

每个操作包含以下属性：

* **名称**：操作组中的唯一标识符。  
* **操作类型**：要执行的操作。 示例包括发送语音呼叫、短信、电子邮件，或者触发各种类型的自动化操作。 请参阅本文下文中的“类型”。 
* **详细信息**：因“操作类型”而异的相应详细信息。 

有关如何使用 Azure 资源管理器模板以配置操作组的信息，请参阅[操作组资源管理器模板](../../azure-monitor/platform/action-groups-create-resource-manager-template.md)。

## <a name="create-an-action-group-by-using-the-azure-portal"></a>使用 Azure 门户创建操作组 ##
1. 在[门户](https://portal.azure.com)中，选择“监视器”。 “监视器”边栏选项卡将所有监视设置和数据合并到一个视图中。

    ![“监视”服务](./media/action-groups/home-monitor.png)
1. 选择“警报”，然后选择“管理操作组”。

    ![“管理操作组”按钮](./media/action-groups/manage-action-groups.png)
1. 选择“添加操作组”，并填写字段。

    ![“添加操作组”命令](./media/action-groups/add-action-group.png)
1. 在“操作组名称”框中输入名称，然后在“短名称”框中输入名称。 使用此组发送通知时，短名称被用来代替完整的操作组名称。

      ![“添加操作组”对话框](./media/action-groups/action-group-define.png)

1. “订阅”框会自动填充当前订阅。 此“订阅”是在其中保存操作组的订阅。

1. 选择在其中保存操作组的“资源组”。

1. 通过为每个操作提供以下项来定义操作列表：

    a. **名称**：输入此操作的唯一标识符。

    b. **操作类型**：选择电子邮件/短信/推送/语音、逻辑应用、Webhook、ITSM 或自动化 Runbook。

    c. **详细信息**：根据操作类型，输入电话号码、电子邮件地址、webhook URI、Azure 应用、ITSM 连接或自动化 runbook。 对于 ITSM 操作，另外指定 ITSM 工具需要的“工作项”和其他字段。

1. 选择“确定”创建操作组。

## <a name="manage-your-action-groups"></a>管理操作组 ##
创建操作组后，它会在“监视器”边栏选项卡的“操作组”部分显示。 选择要管理的操作组：

* 添加、编辑或删除操作。
* 删除操作组。

## <a name="action-specific-information"></a>特定于操作的信息
**Azure 应用推送** - 一个操作组中最多可以有 10 个 Azure 应用操作。 目前，Azure 应用操作只支持 ServiceHealth 警报。 任何其他警报时间都会被忽略。 请参阅[配置每次发布服务运行状况通知时的警报](../../azure-monitor/platform/alerts-activity-log-service-notifications.md)。

**电子邮件** - 将从以下电子邮件地址发送电子邮件。 确保电子邮件筛选正确配置
   - azure-noreply@microsoft.com
   - azureemail-noreply@microsoft.com
   - alerts-noreply@mail.windowsazure.com

一个操作组中最多可以有 1000 个电子邮件操作。 请参阅[速率限制信息](./../../azure-monitor/platform/alerts-rate-limiting.md)一文

**ITSM** - 在需要 ITSM 连接的操作组 ITSM 操作中最多可以有 10 个 ITSM 连接。 了解如何创建 [ITSM 连接](../../azure-monitor/platform/itsmc-overview.md)。

**逻辑应用** - 一个操作组中最多可以有 10 个逻辑应用操作。

**Function App** - 配置为操作的 Function App 的函数密钥通过函数 API 读取，这当前需要 v2 函数应用将应用设置“AzureWebJobsSecretStorageType”配置为“文件”，请参阅[在 Functions V2 中进行密钥管理的更改]( https://aka.ms/funcsecrets)以了解详细信息。

**Runbook** - 一个操作组中最多可以有 10 个 Runbook 操作，有关 Runbook 有效负载的限制，请参考 [Azure 订阅服务限制](../../azure-subscription-service-limits.md)

**短信** - 一个操作组中最多可以有 10 个短信操作，请参阅[速率限制信息](./../../azure-monitor/platform/alerts-rate-limiting.md)一文和[短信警报行为](../../azure-monitor/platform/alerts-sms-behavior.md)一文

**语音** - 一个操作组中最多可以有 10 个语音操作</dd>
请参阅[速率限制信息](./../../azure-monitor/platform/alerts-rate-limiting.md)一文</dd>

**Webhook** - 一个操作组中最多可以有 10 个 Webhook 操作。 重试逻辑 - 响应的超时期限为 10 秒。 当返回的 HTTP 状态代码为 408、429、503、504 或者 HTTP 终结点未响应时，最多可以重试 2 次 Webhook 调用。 首次重试在 10 秒后发生。 第二次（也是最后一次）重试在 100 秒后发生。

源 IP 地址范围
    - 13.106.57.181
    - 13.106.54.3
    - 13.106.54.19
    - 13.106.38.142
    - 13.106.38.148
    - 13.106.57.196

若要接收有关这些 IP 地址更改的更新，建议配置监视有关操作组服务的信息通知的[服务运行状况警报](./../../azure-monitor/platform/service-notifications.md)。


## <a name="next-steps"></a>后续步骤 ##
* 详细了解[短信警报行为](../../azure-monitor/platform/alerts-sms-behavior.md)。  
* 获取[对活动日志警报 webhook 架构的了解](../../azure-monitor/platform/activity-log-alerts-webhook.md)。  
* 了解有关 [ITSM 连接器](../../azure-monitor/platform/itsmc-overview.md)的详细信息
* 详细了解有关警报的[速率限制](../../azure-monitor/platform/alerts-rate-limiting.md)。
* 获取[活动日志警报概述](../../azure-monitor/platform/alerts-overview.md)，了解如何接收警报。  
* 了解如何[配置每次发布服务运行状况通知时的警报](../../azure-monitor/platform/alerts-activity-log-service-notifications.md)。

