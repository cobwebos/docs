---
title: 使用 PagerDuty 配置 Azure 服务运行状况警报 | Microsoft Docs
description: 获取有关发送到 PagerDuty 实例的服务运行状况事件的个性化通知。
author: shawntabrizi
manager: scotthit
editor: ''
services: service-health
documentationcenter: service-health
ms.assetid: ''
ms.service: service-health
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: shtabriz
ms.openlocfilehash: 6e9fcf20d368e270f9af4551c539acd873335498
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
ms.locfileid: "30178906"
---
# <a name="configure-service-health-alerts-with-pagerduty"></a>使用 PagerDuty 配置服务运行状况警报

本文演示如何使用 Webhook 通过 PagerDuty 设置 Azure 服务运行状况通知。 使用 [PagerDuty](https://www.pagerduty.com/) 的自定义 Microsoft Azure 集成类型，可以轻松地将服务运行状况警报添加到新的或现有的 PagerDuty 服务。

## <a name="creating-a-service-health-integration-url-in-pagerduty"></a>在 PagerDuty 中创建服务运行状况集成 URL
1.  请确保已注册并登录到 [PagerDuty](https://www.pagerduty.com/) 帐户。

2.  在 PagerDuty 中导航到“服务”部分。

    ![PagerDuty 中的“服务”部分](./media/webhook-alerts/pagerduty-services-section.png)

3.  选择“添加新服务”或打开已设置的现有服务。

4.  在“集成设置”中，选择以下项：

    a. **集成类型**：Microsoft Azure

    b. **集成名称**：\<名称\>

    ![PagerDuty 中的“集成设置”](./media/webhook-alerts/pagerduty-integration-settings.png)

5.  填写任何其他必填字段，然后选择“添加”。

6.  打开此新集成，复制并保存“集成 URL”。

    ![PagerDuty 中的“集成 URL”](./media/webhook-alerts/pagerduty-integration-url.png)

## <a name="create-an-alert-using-pagerduty-in-the-azure-portal"></a>在 Azure 门户中使用 PagerDuty 创建警报
### <a name="for-a-new-action-group"></a>对于新操作组：
1. 执行[使用 Azure 门户为新操作组创建有关服务运行状况通知的警报](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md)中的步骤 1 到步骤 8。

2. 在“操作”列表中定义：

    a. **操作类型：***Webhook*

    b. **详细信息：** 前面保存的 PagerDuty **集成 URL**。

    c. **名称：** Webhook 的名称、别名或标识符。

3. 警报创建完成后，选择“保存”。

### <a name="for-an-existing-action-group"></a>对于现有操作组：
1. 在 [Azure 门户](https://portal.azure.com/)中，选择“监视”。

2. 在“设置”部分中，选择“操作组”。

3. 找到要编辑的操作组并选择它。

4. 添加到“操作”列表：

    a. **操作类型：***Webhook*

    b. **详细信息：** 前面保存的 PagerDuty **集成 URL**。

    c. **名称：** Webhook 的名称、别名或标识符。

5. 操作组更新完成后，选择“保存”。

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>通过 HTTP POST 请求测试 Webhook 集成
1. 创建要发送的服务运行状况有效负载。 可以在 [Azure 活动日志警报的 Webhook](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md) 中找到示例服务运行状况 Webhook 有效负载。

2. 按如下所示创建 HTTP POST 请求：

    ```
    POST        https://events.pagerduty.com/integration/<IntegrationKey>/enqueue

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
3. 应会收到 `202 Accepted` 并显示包含“事件 ID”的消息。

4. 转到 [PagerDuty](https://www.pagerduty.com/)，确认集成已设置成功。

## <a name="next-steps"></a>后续步骤
- 了解如何[为现有问题管理系统配置 Webhook 通知](service-health-alert-webhook-guide.md)。
- 查看[活动日志警报 webhook 架构](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md)。 
- 了解[服务运行状况通知](../monitoring-and-diagnostics/monitoring-service-notifications.md)。
- 详细了解[操作组](../monitoring-and-diagnostics/monitoring-action-groups.md)。