---
title: 排除 Azure 监视器警报和通知的故障
description: Azure 监视器警报和可能的解决方案的常见问题。
author: ofirmanor
ms.author: ofmanor
ms.topic: reference
ms.date: 03/16/2020
ms.subservice: alerts
ms.openlocfilehash: beb47f961c6f24453bd49aa5807c9d801fc199a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132329"
---
# <a name="troubleshooting-problems-in-azure-monitor-alerts"></a>在 Azure 监视器警报中排除问题 

本文讨论 Azure 监视器警报中的常见问题。

Azure 监视器警报会在监视数据中找到重要条件时主动通知您。 有了警报，你就可以在系统的用户注意到问题之前确定和解决这些问题。 有关警报的详细信息，请参阅 Microsoft [Azure 中的警报概述](alerts-overview.md)。

## <a name="action-or-notification-on-my-alert-did-not-work-as-expected"></a>警报上的操作或通知未按预期工作

如果在 Azure 门户中可以看到已触发的警报，但某些操作或通知出现问题，请参阅以下部分。

## <a name="did-not-receive-expected-email"></a>未收到预期电子邮件

如果在 Azure 门户中可以看到已触发警报，但没有收到已配置的电子邮件，请按照以下步骤操作： 

1. **电子邮件被[操作规则](alerts-action-rules.md)抑制吗**？ 

    通过单击门户中的触发警报进行检查，并查看已抑制[操作组的](action-groups.md)历史记录选项卡： 

    ![警报操作规则抑制历史记录](media/alerts-troubleshoot/history-action-rule.png)

1. **操作类型是否"通过电子邮件发送 Azure 资源管理器角色"？**

    此操作仅查看订阅范围内的 Azure 资源管理器角色分配和*类型"用户*"。  请确保在订阅级别分配了角色，而不是在资源级别或资源组级别分配了角色。

1. **您的电子邮件服务器和邮箱是否接受外部电子邮件？**

    验证以下三个地址的电子邮件是否未被阻止：
      - azure-noreply@microsoft.com  
      - azureemail-noreply@microsoft.com
      - alerts-noreply@mail.windowsazure.com

    通常，内部邮件列表或通讯组列表会阻止来自外部电子邮件地址的电子邮件。 您需要将上述电子邮件地址列入白名单。  
    要进行测试，请向操作组添加常规工作电子邮件地址（而不是邮件列表），并查看警报是否到达该电子邮件。 

1. **电子邮件是由收件箱规则还是垃圾邮件筛选器处理？** 

    验证没有删除这些电子邮件或将其移动到侧文件夹的收件箱规则。 例如，收件箱规则可以捕获主题中的特定发件人或特定单词。

    此外，请检查：
    
      - 电子邮件客户端的垃圾邮件设置（如 Outlook、Gmail）
      - 电子邮件服务器的发件人限制/垃圾邮件设置/隔离设置（如 Exchange、Office 365、G 套件）
      - 电子邮件安全设备的设置（如果有）（如梭子鱼、思科）。 

1. **您是否意外取消订阅操作组？** 

    警报电子邮件提供取消订阅操作组的链接。 要检查是否意外取消订阅此操作组，请执行以下操作：

    1. 打开门户中的操作组并检查"状态"列：

    ![操作组状态列](media/alerts-troubleshoot/action-group-status.png)

    2. 搜索您的电子邮件以查找取消订阅确认：

    ![取消订阅警报操作组](media/alerts-troubleshoot/unsubscribe-action-group.png)

    要再次订阅 - 要么使用您收到的取消订阅确认电子邮件中的链接，要么从操作组中删除电子邮件地址，然后再次添加。 
 
1. **您是否因许多电子邮件访问单个电子邮件地址而被评为受限？** 

    电子邮件[的速率限制](alerts-rate-limiting.md)为每小时不超过 100 封电子邮件到每个电子邮件地址。 如果通过此阈值，将删除其他电子邮件通知。  检查您是否收到一条消息，指出您的电子邮件地址已暂时限制费率： 
 
   ![电子邮件速率受限](media/alerts-troubleshoot/email-paused.png)

   如果要在不限制速率的情况下接收大量通知，请考虑使用不同的操作，如 Webhook、逻辑应用、Azure 函数或自动化 Runbook，这些操作都不是速率限制的。 

## <a name="did-not-receive-expected-sms-voice-call-or-push-notification"></a>未收到预期的短信、语音呼叫或推送通知

如果在门户中可以看到已触发的警报，但没有收到已配置的 SMS、语音呼叫或推送通知，请按照以下步骤操作： 

1. **[操作规则抑制了该操作吗](alerts-action-rules.md)？** 

    通过单击门户中的触发警报进行检查，并查看已抑制[操作组的](action-groups.md)历史记录选项卡： 

    ![警报操作规则抑制历史记录](media/alerts-troubleshoot/history-action-rule.png)

   如果这是无意的，您可以修改、禁用或删除操作规则。
 
1. **短信/语音：您的电话号码是否正确？**

   检查 SMS 操作中有关国家/地区代码或电话号码中的拼写错误。 
 
1. **短信/语音：你被限制率吗？** 

    短信和语音通话的速率限制为每个电话号码每五分钟不超过一次通知。 如果通过此阈值，通知将被删除。 

      - 语音呼叫 – 检查呼叫历史记录，并查看前五分钟是否有来自 Azure 的不同呼叫。 
      - 短信 - 检查您的短信历史记录，以显示您的电话号码已受到限制的短信。 

    如果要在不限制速率的情况下接收大量通知，请考虑使用不同的操作，如 Webhook、逻辑应用、Azure 函数或自动化 Runbook，这些操作都不是速率限制的。 
 
1. **短信：您是否意外取消订阅操作组？**

    打开 SMS 历史记录，并检查您是否选择退出此特定操作组（使用禁用action_group_short_name回复）或所有操作组（使用停止回复）发送短信。 要再次订阅，请发送相关的 SMS 命令（启用action_group_short_name或 START），或从操作组中删除 SMS 操作，然后再次添加它。  有关详细信息，请参阅[操作组中的 SMS 警报行为](alerts-sms-behavior.md)。

1. **您是否不小心阻止了手机上的通知？**

   大多数移动电话都允许您阻止来自特定电话号码或短代码的呼叫或短信，或阻止来自特定应用（如 Azure 移动应用）的推送通知。 要检查是否意外阻止了手机上的通知，请搜索特定于手机操作系统和型号的文档，或使用其他电话和电话号码进行测试。 

## <a name="expected-another-type-of-action-to-trigger-but-it-did-not"></a>预期将触发另一种类型的操作，但它没有 

如果在门户中可以看到已触发的警报，但其配置的操作未触发，请按照以下步骤操作： 

1. **操作规则抑制了该操作吗？** 

    通过单击门户中的触发警报进行检查，并查看已抑制[操作组的](action-groups.md)历史记录选项卡： 

    ![警报操作规则抑制历史记录](media/alerts-troubleshoot/history-action-rule.png)
 
    如果这是无意的，您可以修改、禁用或删除操作规则。 

1. **网钩没有触发吗？**

    1. **源 IP 地址已被阻止吗？**
    
       将网络钩子的[IP 地址](action-groups.md#action-specific-information)列入白名单。

    1. **Webhook 终结点是否正常工作？**

       验证已配置的 Webhook 终结点正确且终结点工作正常。 检查 Webhook 日志或检测其代码，以便进行调查（例如，记录传入的有效负载）。 

    1. **你是在给Slack或微软团队打电话？**  
    每个终结点都需要特定的 JSON 格式。 请按照[这些说明](action-groups-logic-app.md)配置逻辑应用操作。

    1. **您的 Webhook 是否变得无响应或返回错误？** 

        我们 Webhook 响应的超时时间是 10 秒。 返回以下 HTTP 状态代码时，最多重试两次 Webhook 调用：408、429、503、504 或 HTTP 终结点未响应。 首次重试在 10 秒后发生。 第二次也是最后一次重试在 100 秒后进行。 如果第二次重试失败，则在 30 分钟内不会再次调用该终结点，不管执行什么操作。

## <a name="action-or-notification-happened-more-than-once"></a>多次执行操作或通知 

如果您多次收到警报通知（如电子邮件或 SMS），或者警报的操作（如 Webhook 或 Azure 函数）已多次触发，请按照以下步骤操作： 

1. **真的是同一个警报吗？** 

    在某些情况下，大约同时触发多个类似的警报。 因此，它可能只是像同一个警报触发了它的行动多次。 例如，活动日志警报规则可能配置为在事件启动时和事件完成（成功或失败）时触发，因为不筛选事件状态字段。 

    要检查这些操作或通知是否来自不同警报，请检查警报详细信息，例如其时间戳和警报 ID 或其相关 ID。或者，检查门户中已触发警报的列表。 如果是这种情况，则需要调整警报规则逻辑或以其他方式配置警报源。 

1. **该操作是否在多个操作组中重复？** 

    触发警报时，将独立处理其每个操作组。 因此，如果操作（如电子邮件地址）出现在多个触发的操作组中，则每个操作组将调用一次。 

    要检查触发了哪些操作组，请检查警报历史记录选项卡。您将看到警报规则中定义的操作组和按操作规则添加到警报的操作组： 

    ![在多个操作组中重复的操作](media/alerts-troubleshoot/action-repeated-multi-action-groups.png)

## <a name="action-or-notification-has-an-unexpected-content"></a>操作或通知具有意外内容

如果您已收到警报，但认为其某些字段丢失或不正确，请按照以下步骤操作： 

1. **您是否为操作选择了正确的格式？** 

    每个操作类型（电子邮件、Webhook 等）有两种格式 - 默认格式、旧格式和[较新的通用架构格式](alerts-common-schema.md)。 创建操作组时，可以指定每个操作所需的格式 - 操作组中的不同操作可能具有不同的格式。 

    例如，对于 Webhook 操作： 

    ![webhook 操作架构选项](media/alerts-troubleshoot/webhook.png)

    检查在操作级别指定的格式是否达到预期。 例如，您可能已经开发了响应警报（Webhook、函数、逻辑应用等）的代码，期望使用一种格式，但在稍后的操作中，您或其他人指定了不同的格式。  

    此外，检查活动[日志警报](activity-log-alerts-webhook.md)的有效负载格式 （JSON），检查[日志搜索警报](alerts-log-webhook.md)（应用程序见解和日志分析）、[指标警报](alerts-metric-near-real-time.md#payload-schema)、[常见警报架构](alerts-common-schema-definitions.md)以及弃用[的经典指标警报](alerts-webhooks.md)。

 
1. **活动日志警报：活动日志中的信息是否可用？** 

    [活动日志警报](activity-log-alerts.md)是基于写入 Azure 活动日志的事件的警报，例如有关创建、更新或删除 Azure 资源的事件、服务运行状况和资源运行状况事件，或 Azure 顾问和 Azure 策略的调查结果。 如果收到基于活动日志的警报，但需要的某些字段丢失或不正确，请先检查活动日志本身中的事件。 如果 Azure 资源未在其活动日志事件中写入要查找的字段，则这些字段将不会包含在相应的警报中。 

## <a name="action-rule-is-not-working-as-expected"></a>操作规则未按预期工作 

如果在门户中可以看到已触发的警报，但相关操作规则未按预期工作，请按照以下步骤操作： 

1. **操作规则是否启用？** 

    检查操作规则状态列以验证是否启用了相关操作角色。 

    ![图形](media/alerts-troubleshoot/action-rule-status.png) 

    如果未启用，则可以通过选择操作规则并单击"启用"来启用该操作规则。 

1. **是服务运行状况警报吗？** 

    服务运行状况警报（监视器服务 ="服务运行状况"）不受操作规则的影响。 

1. **操作规则对警报起作用了吗？** 

    通过单击门户中的已触发警报，检查操作规则是否处理了警报，并查看历史记录选项卡。

    下面是禁止所有操作组的操作规则的示例： 
 
     ![警报操作规则抑制历史记录](media/alerts-troubleshoot/history-action-rule.png)

    下面是添加另一个操作组的操作规则的示例：

    ![在多个操作组中重复的操作](media/alerts-troubleshoot/action-repeated-multi-action-groups.png)
 

1. **操作规则范围和筛选器是否与触发的警报匹配？** 

    如果您认为操作规则应该已触发，但未触发，或者它不应触发，但确实触发了，请仔细检查操作规则范围和筛选条件与触发警报的属性。 


## <a name="how-to-find-the-alert-id-of-a-fired-alert"></a>如何查找已触发警报的警报 ID

打开有关特定触发警报的案例时（例如，如果您未收到其电子邮件通知），则需要提供警报 ID。 

要找到它，请按照以下步骤操作：

1. 在 Azure 门户中，导航到已触发警报的列表，并查找该特定警报。 您可以使用筛选器来帮助找到它。 

1. 单击警报以打开警报详细信息。 

1. 向下滚动到第一个选项卡（摘要选项卡）的警报字段中，直到找到它，然后复制它。 该字段还包括一个"复制到剪贴板"帮助器按钮，您可以使用。  

    ![查找警报 ID](media/alerts-troubleshoot/get-alert-id.png)

## <a name="problem-creating-updating-or-deleting-action-rules-in-the-azure-portal"></a>在 Azure 门户中创建、更新或删除操作规则时遇到问题

如果在尝试创建、更新或删除[操作规则](alerts-action-rules.md)时收到错误，请按照以下步骤操作： 

1. **您是否收到权限错误？**  

    您应该具有 [监视参与者内置角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor)，或者具有与操作规则和警报相关的特定权限。

1. **是否验证操作规则参数？**  

    检查[操作规则文档](alerts-action-rules.md)，或[操作规则 PowerShell 集-AzActionRule 命令](https://docs.microsoft.com/powershell/module/az.alertsmanagement/Set-AzActionRule?view=azps-3.5.0)。 


## <a name="next-steps"></a>后续步骤
- 如果使用日志警报，请参阅[故障排除日志警报](alert-log-troubleshoot.md)。
- 返回 Azure[门户](https://portal.azure.com)，检查是否通过上述指南解决了问题 
