---
title: 排查 Azure Monitor 警报和通知问题
description: Azure Monitor 警报的常见问题和可能的解决方法。
author: ofirmanor
ms.author: ofmanor
ms.topic: reference
ms.date: 03/16/2020
ms.subservice: alerts
ms.openlocfilehash: d86c6d8b67546aede86a3fc3c905f6feb40d92db
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91403409"
---
# <a name="troubleshooting-problems-in-azure-monitor-alerts"></a>排查 Azure Monitor 警报的问题

本文讨论 Azure Monitor 警报和通知的常见问题。

在监视数据中发现重要情况时，Azure Monitor 警报会主动通知你。 有了警报，你就可以在系统的用户注意到问题之前确定和解决这些问题。 有关警报的详细信息，请参阅 [Microsoft Azure 中的警报概述](alerts-overview.md)。

如果你遇到警报触发问题或警报未按预期触发问题，请参阅下面的文章。 可以在 Azure 门户中查看已触发的警报。

- [对 Microsoft Azure 中 Azure Monitor 指标警报进行故障排除](alerts-troubleshoot-metric.md)  
- [对 Microsoft Azure 中 Azure Monitor 日志警报进行故障排除](alerts-troubleshoot-metric.md)

如果警报根据 Azure 门户按预期触发，但未出现正确的通知，请使用本文其余部分中的信息来解决该问题。

## <a name="action-or-notification-on-my-alert-did-not-work-as-expected"></a>针对警报的操作或通知未按预期方式进行

如果你可以在 Azure 门户中看到触发的警报，但其某些操作或通知存在问题，请参阅后面的部分。

## <a name="did-not-receive-expected-email"></a>未收到预期的电子邮件

如果你可以在 Azure 门户中看到触发的警报，但未收到你已为其配置的相关电子邮件，请遵循以下步骤：

1. **是否某个[操作规则](alerts-action-rules.md)阻止了该电子邮件**？

    在门户中单击触发的警报进行检查，查看“历史记录”选项卡中是否有已阻止的[操作组](action-groups.md)：

    ![警报操作规则 -“阻止”历史记录](media/alerts-troubleshoot/history-action-rule.png)

1. **操作类型是否为“向 Azure 资源管理器角色发送电子邮件”？**

    此操作仅查看订阅范围的、类型为“用户”的 Azure 资源管理器角色分配。  请确保已在订阅级别分配角色，而不是在资源级别或资源组级别。

1. **电子邮件服务器和邮箱是否接受外部电子邮件？**

    验证来自这三个地址的电子邮件是否未被阻止：
      - azure-noreply@microsoft.com  
      - azureemail-noreply@microsoft.com
      - alerts-noreply@mail.windowsazure.com

    内部邮件列表或通讯组列表阻止来自外部电子邮件地址的电子邮件的情况很常见。 必须允许来自上述电子邮件地址的邮件。  
    若要进行测试，请将一个常规工作电子邮件地址（不是邮件列表）添加到操作组，并查看警报是否到达该电子邮件地址。

1. **相关电子邮件是否被收件箱规则或垃圾邮件筛选器处理了？**

    请确保没有任何收件箱规则会删除这些电子邮件或将其移动到副文件夹。 例如，收件箱规则可能会捕捉特定发件人或主题中的特定字词。

    另请检查：

   - 电子邮件客户端（如 Outlook、Gmail）的垃圾邮件设置
      - 电子邮件服务器的发件人限制/垃圾邮件设置/隔离设置 (如 Exchange、Microsoft 365、G 套件) 
      - 若使用了电子邮件安全设备（如 Barracuda、Cisco），请检查其设置。

1. **是否意外取消了对操作组的订阅？**

    警报电子邮件提供从操作组取消订阅的链接。 若要检查是否已意外地从此操作组取消订阅，请执行以下操作之一：

    1. 在门户中打开操作组，并检查“状态”列：

    ![操作组状态列](media/alerts-troubleshoot/action-group-status.png)

    2. 搜索电子邮件中是否有取消订阅确认信息：

    ![从警报操作组取消订阅](media/alerts-troubleshoot/unsubscribe-action-group.png)

    若要再次订阅，请使用收到的取消订阅确认电子邮件中的链接，或从操作组中删除电子邮件地址，然后再重新添加。 
 
1. **是否由于很多电子邮件都发送到一个电子邮件地址而受到速率限制？**

    电子邮件有[速率限制](alerts-rate-limiting.md)，发送给每个电子邮件地址的电子邮件不得超过每小时 100 封。 如果超过此阈值，则会删除其他电子邮件通知。  请检查是否已收到一封表明电子邮件地址暂时存在速率限制的邮件： 
 
   ![电子邮件速率限制](media/alerts-troubleshoot/email-paused.png)

   如果你想要在没有速率限制的情况下接收大量通知，请考虑使用不同的操作，如 webhook、逻辑应用、Azure 函数或自动化 runbook，这些都没有速率限制。 

## <a name="did-not-receive-expected-sms-voice-call-or-push-notification"></a>未收到预期的短信、语音呼叫或推送通知

如果你可以在门户中看到触发的警报，但未收到你已为其配置的相关短信、语音呼叫或推送通知，请遵循以下步骤： 

1. **是否某个[操作规则](alerts-action-rules.md)阻止了该操作？**

    在门户中单击触发的警报进行检查，查看“历史记录”选项卡中是否有已阻止的[操作组](action-groups.md)： 

    ![警报操作规则 -“阻止”历史记录](media/alerts-troubleshoot/history-action-rule.png)

   如果是无意中阻止了操作组，可以修改、禁用或删除操作规则。
 
1. **短信/语音：你的电话号码是否正确？**

   检查短信操作，以查明国家/地区代码或电话号码中是否有拼写错误。
 
1. **短信/语音：你是否受到了速率限制？**

    对于每个电话号码，短信和语音呼叫的速率限制是每 5 分钟不超过 1 个通知。 如果超过此阈值，则将丢弃通知。

      - 语音呼叫 – 查看呼叫历史记录，了解你在过去 5 分钟内是否有来自 Azure 的不同呼叫。
      - 短信 - 查看短信历史记录，了解其中是否有一条短信表明你的电话号码已受到速率限制。

    如果你想要在没有速率限制的情况下接收大量通知，请考虑使用不同的操作，如 webhook、逻辑应用、Azure 函数或自动化 runbook，这些都没有速率限制。 
 
1. **短信：是否意外取消了对操作组的订阅？**

    打开短信历史记录，检查是否已选择禁止传送来自此特定操作组（使用“DISABLE action_group_short_name”回复）或所有操作组（使用“STOP”回复）的短信。 若要再次订阅，请发送相关短信命令（ENABLE action_group_short_name 或 START），或从操作组中删除短信操作，然后重新添加。  有关详细信息，请参阅[操作组中的短信警报行为](alerts-sms-behavior.md)。

1. **你的手机上是否意外阻止了通知？**

   大多数移动电话允许阻止来自特定电话号码或短代码的呼叫或短信，或阻止来自特定应用（如 Azure 移动应用）的推送通知。 若要检查你的手机上是否意外阻止了通知，请搜索特定于你的手机操作系统和型号的文档，或使用其他手机和电话号码进行测试。

## <a name="expected-another-type-of-action-to-trigger-but-it-did-not"></a>预期另一类型的操作会触发，但它未触发 
   
如果可以在门户中看到触发的警报，但其配置的操作未触发，请遵循以下步骤：

1. **是否某个操作规则阻止了该操作？**

    在门户中单击触发的警报进行检查，查看“历史记录”选项卡中是否有已阻止的[操作组](action-groups.md)：

    ![警报操作规则 -“阻止”历史记录](media/alerts-troubleshoot/history-action-rule.png)
 
    如果是无意中阻止了操作组，可以修改、禁用或删除操作规则。

1. **Webhook 是否未触发？**

    1. **是否已阻止源 IP 地址？**
    
       将需从其调用 Webhook 的 [IP 地址](action-groups.md#action-specific-information)加入允许列表。

    1. **你的 Webhook 终结点是否正常工作？**

       验证已配置的 Webhook 终结点是否正确，以及该终结点是否在正常运行。 检查 Webhook 日志或检测其代码，以便进行调查（例如，记录传入的有效负载）。

    1. **你是在调用可宽延时间还是 Microsoft 团队？**  
    这些终结点中的每一个都需要特定的 JSON 格式。 请按照[这些说明](action-groups-logic-app.md)来配置逻辑应用操作。

    1. **Webhook 是否已停止响应或返回错误？** 

        Webhook 响应的超时期限为 10 秒。 当返回以下 HTTP 状态代码时，或者当 HTTP 终结点不响应时，最多会再重试 Webhook 调用两次：408、429、503、504。 首次重试在 10 秒后发生。 第二次（也是最后一次）重试在 100 秒后发生。 如果第二次重试失败，则在 30 分钟内不会再次调用该终结点，不管执行什么操作。

## <a name="action-or-notification-happened-more-than-once"></a>操作或通知多次发生 

如果多次收到针对某个警报的通知（如电子邮件或短信），或者该警报的操作（如 webhook 或 Azure 函数）已触发了多次，请遵循以下步骤： 

1. **它是否确实是同一警报？** 

    在某些情况下，会几乎同时触发多个类似的警报。 因此，看起来可能就会像同一警报多次触发了其操作。 例如，活动日志警报规则可能会配置为在事件已启动时以及在事件已完成（成功或失败）时触发（不筛选事件状态字段）。 

    若要检查这些操作或通知是否来自不同警报，请查看警报详细信息，如警报时间戳以及警报 ID 或其相关 ID。或者，在门户中查看触发的警报的列表。 如果是这种情况，则需要调整警报规则逻辑或配置警报源。 

1. **此操作是否在多个操作组中重复？** 

    触发警报时，其每个操作组都是单独处理的。 因此，如果某个操作（如电子邮件地址）出现在多个触发的操作组中，则每个操作组都会调用该操作一次。 

    若要检查触发了哪些操作组，请查看警报的“历史记录”选项卡。在该选项卡中可以看到在警报规则中定义的操作组，以及通过操作规则添加到警报的操作组： 

    ![操作在多个操作组中重复](media/alerts-troubleshoot/action-repeated-multi-action-groups.png)

## <a name="action-or-notification-has-an-unexpected-content"></a>操作或通知存在意外的内容

如果收到了警报，但认为警报的某些字段丢失或不正确，请按照以下步骤操作： 

1. **是否为操作选择了正确的格式？** 

    每个操作类型 (电子邮件、webhook 等 ) 具有两种格式：默认格式、旧格式和 [较新的通用架构格式](alerts-common-schema.md)。 创建操作组时，请指定每个操作所需的格式，操作组中的不同操作可能具有不同的格式。 

    例如，对于 Webhook 操作： 

    ![Webhook 操作架构选项](media/alerts-troubleshoot/webhook.png)

    检查在操作级别指定的格式是否是你需要的格式。 例如，你可能已开发响应警报的代码（Webhook、函数、逻辑应用等），这些代码需要一种格式，但是稍后你或其他人在操作中指定了另一种格式。  

    另外，检查[活动日志警报](activity-log-alerts-webhook.md)、[日志搜索警报](alerts-log-webhook.md)（Application Insights 和日志分析）、[指标警报](alerts-metric-near-real-time.md#payload-schema)、[通用警报架构](alerts-common-schema-definitions.md)以及已弃用的[经典指标警报](alerts-webhooks.md)的有效负载格式 (JSON)。

 
1. **活动日志警报：活动日志中是否有该信息？** 

    [活动日志警报](activity-log-alerts.md)是基于写入到 Azure 活动日志的事件（例如，有关创建、更新或删除 Azure 资源的事件、服务运行状况和资源运行状况事件，或者 Azure 顾问和 Azure Policy 发现的情况）的警报。 如果你收到了基于活动日志的警报，但所需的某些字段丢失或不正确，请首先检查活动日志本身中的事件。 如果 Azure 资源未在其活动日志事件中写入所需字段，则这些字段将不会包含在相应的警报中。 

## <a name="action-rule-is-not-working-as-expected"></a>操作规则未按预期发挥作用 

如果可以在门户中看到触发的警报，但相关的操作规则未按预期发挥作用，请遵循以下步骤： 

1. **操作规则是否已启用？** 

    检查“操作规则状态”列以验证是否已启用相关操作角色。 

    ![图形](media/alerts-troubleshoot/action-rule-status.png) 

    如果它未启用，则可以通过选择操作规则并单击“启用”来启用它。 

1. **它是否为服务运行状况警报？** 

    服务运行状况警报（监视服务 =“服务运行状况”）不受操作规则影响。 

1. **操作规则对你的警报是否起作用？** 

    在门户中单击触发的警报检查该操作规则是否已处理你的警报，并查看“历史记录”选项卡。

    下面是一个阻止所有操作组的操作规则示例： 
 
     ![警报操作规则 -“阻止”历史记录](media/alerts-troubleshoot/history-action-rule.png)

    下面是一个添加另一操作组的操作规则示例：

    ![操作在多个操作组中重复](media/alerts-troubleshoot/action-repeated-multi-action-groups.png)
 

1. **操作规则范围和筛选器是否与触发的警报匹配？** 

    如果你认为操作规则本来应触发但未触发，或者它本来不应触发但确实已触发，请仔细检查操作规则作用域和筛选条件与触发的警报的属性。 


## <a name="how-to-find-the-alert-id-of-a-fired-alert"></a>如何查找已触发的警报的警报 ID

当打开有关特定发出的警报的事例时 (例如–如果未收到其电子邮件通知) ，则需要提供警报 ID。 

若要查找该 ID，请遵循以下步骤：

1. 在 Azure 门户中，导航到已触发的警报列表，并查找该特定警报。 可以使用筛选器来帮助查找。 

1. 单击该警报打开警报详细信息。 

1. 在第一个选项卡（摘要选项卡）的警报字段中向下滚动，直到找到该 ID，然后复制它。 该字段还包含一个“复制到剪贴板”帮助器按钮。  

    ![查找警报 ID](media/alerts-troubleshoot/get-alert-id.png)

## <a name="problem-creating-updating-or-deleting-action-rules-in-the-azure-portal"></a>在 Azure 门户中创建、更新或删除操作规则时出现问题

如果你在尝试创建、更新或删除[操作规则](alerts-action-rules.md)时遇到错误，请遵循以下步骤： 

1. **是否遇到了权限错误？**  

    你应该拥有 [监视参与者内置角色](../../role-based-access-control/built-in-roles.md#monitoring-contributor)，或者与操作规则和警报相关的特定权限。

1. **是否已验证操作规则参数？**  

    查看[操作规则文档](alerts-action-rules.md)或[操作规则 PowerShell Set-AzActionRule](/powershell/module/az.alertsmanagement/set-azactionrule?view=azps-3.5.0) 命令。 


## <a name="next-steps"></a>后续步骤
- 如果使用的是日志警报，另请参阅[排查日志警报问题](./alerts-troubleshoot-log.md)。
- 返回 [Azure 门户](https://portal.azure.com)，检查是否已遵循上述指导解决了问题 
