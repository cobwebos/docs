---
title: "OMS Log Analytics 中的 Webhook 警报操作示例 | Microsoft 文档"
description: "响应 Log Analytics 警报可以运行的操作之一是 *Webhook*，它令你可以通过单个 HTTP 请求调用外部进程。 本文将为你逐步讲解如何使用 Slack 在 Log Analytics 警报中创建 Webhook 操作。"
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 13c39f0f-fd3c-472d-8324-ddf7538be45e
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2017
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: c8e67f2dcd061e3cd92eec40d6e6bb3de5d73b67
ms.openlocfilehash: 55b66132f7ec5c26c0a7cac1ec0a5c403dbd1082
ms.lasthandoff: 03/01/2017


---

# <a name="create-an-alert-webhook-action-in-oms-log-analytics-to-send-message-to-slack"></a>在 OMS Log Analytics 中创建警报 webhook 操作以将消息发送到 Slack
响应 [Log Analytics 警报](log-analytics-alerts.md)可以运行的操作之一是 *Webhook*，它令你可以通过单个 HTTP 请求调用外部进程。  如需了解警报和 Webhook 的详细信息，请参阅 [Log Analytics 中的警报](log-analytics-alerts.md)

在本文中，我们将为你逐步讲解如何使用消息服务 Slack 在 Log Analytics 警报中创建 Webhook 操作。

> [!NOTE]
> 必须具有 Slack 帐户才能完成本示例。  如果没有，可以在 [slack.com](http://slack.com) 注册免费帐户。
> 
> 

## <a name="step-1---enable-webhooks-in-slack"></a>步骤 1 - 在 Slack 中启用 Webhook
1. 登录到 [slack.com](http://slack.com) 中的 Slack。
2. 在左窗格中选中“**通道**”部分中的通道。  这是接收消息的通道。  可以选择默认通道之一，如“**常规**”或“**随机**”。  在生产方案中，最可能会创建特殊通道，如 **criticalservicealerts**。 <br>
   
   ![Slack 通道](media/log-analytics-alerts-webhooks/oms-webhooks01.png)
3. 单击“**添加一个应用程序或自定义集成**”打开该应用程序目录。
4. 在搜索框中键入 *Webhook*，然后选择“**传入 Webhook**”。 <br>
   
   ![Slack 通道](media/log-analytics-alerts-webhooks/oms-webhooks02.png)
5. 单击团队名称旁边的“**安装**”。
6. 单击“**添加配置**”。
7. 选择将用于此示例的通道，然后单击“**添加传入 Webhook 集成**”。  
8. 复制 **Webhook URL**。  将此粘贴到警报配置。 <br>
   
    ![Slack 通道](media/log-analytics-alerts-webhooks/oms-webhooks05.png)

## <a name="step-2---create-alert-rule-in-log-analytics"></a>步骤 2 - 在 Log Analytics 中创建警报规则
1. 使用以下设置[创建警报规则](log-analytics-alerts.md)。
   * 查询：```    Type=Event EventLevelName=error ```
   * 检查此警报的时间间隔：5 分钟
   * 结果数：大于 10
   * 时间段：60 分钟
   * 为 **Webhook** 选择“**是**”，为其他操作选择“**否**”。
2. 将 Slack URL 粘贴到“**Webhook URL**”字段。
3. 选择此选项以**包括自定义 JSON 负载**。
4. Slack 预期为 JSON 格式的负载，带有名为 *text* 的参数 。  这是它将显示在其创建的消息中的文本。  可以通过 *#* 符号使用一个或多个警报参数，如以下示例所示。
   
    ```
    {
    "text":"#alertrulename fired with #searchresultcount records which exceeds the over threshold of #thresholdvalue ."
    }
    ```
   
    ![示例 JSON 负载](media/log-analytics-alerts-webhooks/oms-webhooks07.png)
5. 单击“**保存**”以保存警报规则。
6. 等待足够的时间创建警报，然后再检查用于类似如下消息的 Slack。
   
   ![Slack 中的示例 Webhook](media/log-analytics-alerts-webhooks/oms-webhooks08.png)

### <a name="advanced-webhook-payload-for-slack"></a>Slack 的高级 Webhook 负载
可以使用 Slack 广泛自定义入站消息。 有关详细信息，请参阅 Slack 网站上的“[传入 Webhook](https://api.slack.com/incoming-webhooks)”。 以下是更复杂的负载，以创建带有格式的丰富消息：

    {
        "attachments": [
            {
                "title":"OMS Alerts Custom Payload",
                "fields": [
                    {
                        "title": "Alert Rule Name",
                        "value": "#alertrulename"},
                    {
                        "title": "Link To SearchResults",
                        "value": "<#linktosearchresults|OMS Search Results>"},
                    {
                        "title": "Search Interval",
                        "value": "#searchinterval"},
                    {
                        "title": "Threshold Operator",
                        "value": "#thresholdoperator"},
                    {
                        "title": "Threshold Value",
                        "value": "#thresholdvalue"}
                ],
                "color": "#F35A00"
            }
        ]
    }


这将在 Slack 中生成类似如下的消息。

![Slack 中的示例消息](media/log-analytics-alerts-webhooks/oms-webhooks09.png)

## <a name="summary"></a>摘要
有了此警报规则，每次满足条件时都会向 Slack 发送消息。  

这是为响应警报唯一可以创建的操作示例。  可以创建调用另一个外部服务的 Webhook 操作、在 Azure 自动化中启动 Runbook 的 Runbook 操作，或要将邮件发送给你自己或其他收件人的电子邮件操作。   

## <a name="next-steps"></a>后续步骤
* 了解 [Log Analytics 中的其他警报操作](log-analytics-alerts-actions.md)，包括其他操作。



