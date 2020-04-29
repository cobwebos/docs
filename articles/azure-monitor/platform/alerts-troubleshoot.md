---
title: Azure Monitor 警报和通知疑难解答
description: Azure Monitor 警报和可能的解决方案的常见问题。
author: ofirmanor
ms.author: ofmanor
ms.topic: reference
ms.date: 03/16/2020
ms.subservice: alerts
ms.openlocfilehash: beb47f961c6f24453bd49aa5807c9d801fc199a3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80132329"
---
# <a name="troubleshooting-problems-in-azure-monitor-alerts"></a>排查 Azure Monitor 警报中的问题 

本文讨论 Azure Monitor 警报中的常见问题。

当在监视数据中发现重要条件时，Azure Monitor 警报会主动通知你。 有了警报，你就可以在系统的用户注意到问题之前确定和解决这些问题。 有关警报的详细信息，请参阅[Microsoft Azure 中的警报概述](alerts-overview.md)。

## <a name="action-or-notification-on-my-alert-did-not-work-as-expected"></a>警报上的操作或通知未按预期方式工作

如果你可以在 Azure 门户中看到触发的警报，但某些操作或通知有问题，请参阅以下部分。

## <a name="did-not-receive-expected-email"></a>未收到预期电子邮件

如果你可以在 Azure 门户中看到已激发的警报，但未收到你已配置的电子邮件，请执行以下步骤： 

1. **[操作规则](alerts-action-rules.md)是否取消了电子邮件**？ 

    在门户中单击触发的警报，并查看取消[操作组](action-groups.md)的 "历史记录" 选项卡： 

    ![警报操作规则禁止显示历史记录](media/alerts-troubleshoot/history-action-rule.png)

1. **操作类型是 "电子邮件 Azure 资源管理器角色" 吗？**

    此操作仅查看 Azure 资源管理器在订阅作用域和类型为*User*的角色分配。  请确保已在订阅级别分配角色，而不是在资源级别或资源组级别分配角色。

1. **电子邮件服务器和邮箱是否接受外部电子邮件？**

    验证来自这三个地址的电子邮件是否未被阻止：
      - azure-noreply@microsoft.com  
      - azureemail-noreply@microsoft.com
      - alerts-noreply@mail.windowsazure.com

    内部邮件列表或通讯组列表通常会阻止来自外部电子邮件地址的电子邮件。 需要将上述电子邮件地址列入允许列表。  
    若要进行测试，请将常规工作电子邮件地址（而非邮件列表）添加到操作组，并查看警报是否到达该电子邮件。 

1. **电子邮件是否由收件箱规则或垃圾邮件筛选器处理？** 

    验证没有删除这些电子邮件或将其移动到侧文件夹的收件箱规则。 例如，收件箱规则可能会捕捉特定发件人或主题中的特定词。

    另外，请检查：
    
      - 电子邮件客户端（如 Outlook、Gmail）的垃圾邮件设置
      - 电子邮件服务器的发件人限制/垃圾邮件设置/隔离设置（如 Exchange、Office 365、G 套件）
      - 电子邮件安全设备（如 Barracuda、Cisco）的设置。 

1. **是否意外取消了操作组的订阅？** 

    警报电子邮件提供从操作组取消订阅的链接。 若要检查是否已从该操作组中意外取消订阅，请执行以下操作之一：

    1. 在门户中打开操作组，并检查 "状态" 列：

    ![操作组状态列](media/alerts-troubleshoot/action-group-status.png)

    2. 搜索电子邮件中的取消订阅确认：

    ![从警报操作组取消订阅](media/alerts-troubleshoot/unsubscribe-action-group.png)

    若要再次订阅，请使用收到的取消订阅确认电子邮件中的链接，或从操作组中删除电子邮件地址，然后再重新添加。 
 
1. **由于很多电子邮件都发送到一个电子邮件地址，你的评级是否有限？** 

    每隔一小时向每个电子邮件地址发送电子邮件[速率限制](alerts-rate-limiting.md)为100个电子邮件。 如果你传递此阈值，则会删除其他电子邮件通知。  检查是否已收到一条消息，指出你的电子邮件地址已暂时达到限制： 
 
   ![电子邮件费率限制](media/alerts-troubleshoot/email-paused.png)

   如果你想要在没有速率限制的情况下接收大量通知，请考虑使用不同的操作，如 webhook、逻辑应用、Azure 函数或自动化 runbook，两者都没有速率限制。 

## <a name="did-not-receive-expected-sms-voice-call-or-push-notification"></a>未收到预期的短信、语音呼叫或推送通知

如果你可以在门户中看到触发的警报，但未收到你已配置的 SMS、语音呼叫或推送通知，请执行以下步骤： 

1. **操作[规则](alerts-action-rules.md)是否取消了操作？** 

    在门户中单击触发的警报，并查看取消[操作组](action-groups.md)的 "历史记录" 选项卡： 

    ![警报操作规则禁止显示历史记录](media/alerts-troubleshoot/history-action-rule.png)

   如果无意间，可以修改、禁用或删除操作规则。
 
1. **短信/语音：电话号码是否正确？**

   在国家/地区代码或电话号码中检查 SMS 操作是否有拼写错误。 
 
1. **短信/语音：你的速率是否有限制？** 

    对于每个电话号码，短信和语音呼叫的速率限制为每五分钟一次。 如果传递此阈值，则将删除通知。 

      - 语音呼叫–查看呼叫历史记录，并查看你在过去五分钟内是否有不同的 Azure 呼叫。 
      - SMS-检查 SMS 历史记录中是否存在消息，指示你的电话号码已得到评级。 

    如果你想要在没有速率限制的情况下接收大量通知，请考虑使用不同的操作，如 webhook、逻辑应用、Azure 函数或自动化 runbook，两者都没有速率限制。 
 
1. **SMS：是否意外取消了从操作组的订阅？**

    打开 SMS 历史记录，并检查是否已选择不使用此特定操作组（使用 "禁用" action_group_short_name "答复"）或 "所有操作" 组中的 SMS 传递（使用 "停止" 答复）。 若要再次订阅，请发送相关 SMS 命令（启用 action_group_short_name 或启动），或从操作组中删除 SMS 操作，然后重新添加。  有关详细信息，请参阅[操作组中的短信警报行为](alerts-sms-behavior.md)。

1. **你的手机上是否意外阻止了通知？**

   大多数移动电话允许阻止呼叫或短信的电话号码或短信，或阻止来自特定应用（如 Azure 移动应用）的推送通知。 若要检查你的手机上是否意外阻止了通知，请搜索特定于你的手机操作系统和型号的文档，或使用其他电话号码和电话号码进行测试。 

## <a name="expected-another-type-of-action-to-trigger-but-it-did-not"></a>需要触发另一种类型的操作，但它不是 

如果你可以在门户中看到触发的警报，但其配置的操作未触发，请执行以下步骤： 

1. **操作规则是否取消了操作？** 

    在门户中单击触发的警报，并查看取消[操作组](action-groups.md)的 "历史记录" 选项卡： 

    ![警报操作规则禁止显示历史记录](media/alerts-troubleshoot/history-action-rule.png)
 
    如果无意间，可以修改、禁用或删除操作规则。 

1. **Webhook 是否触发？**

    1. **是否已阻止源 IP 地址？**
    
       将从其调用 webhook 的[IP 地址](action-groups.md#action-specific-information)列入允许列表。

    1. **你的 webhook 终结点是否正常工作？**

       验证配置的 webhook 终结点是否正确，终结点是否正常工作。 检查 webhook 日志或检测其代码，以便进行调查（例如，记录传入有效负载）。 

    1. **你是在调用可宽延时间还是 Microsoft 团队？**  
    其中每个终结点都需要一个特定的 JSON 格式。 请遵照[这些说明](action-groups-logic-app.md)来配置逻辑应用操作。

    1. **Webhook 是无响应还是返回错误？** 

        Webhook 响应的超时期限为10秒。 返回以下 HTTP 状态代码时，webhook 调用将重试最多两次：408、429、503、504或 HTTP 终结点未响应。 首次重试在 10 秒后发生。 第二次和最后一次重试在100秒后发生。 如果第二次重试失败，则在 30 分钟内不会再次调用该终结点，不管执行什么操作。

## <a name="action-or-notification-happened-more-than-once"></a>发生了多次操作或通知 

如果多次收到警报（如电子邮件或短信）的通知，或者触发了多次警报操作（如 webhook 或 Azure function），请遵循以下步骤： 

1. **是否确实是相同的警报？** 

    在某些情况下，会同时触发多个类似的警报。 因此，它可能看似同一警报多次触发了其操作。 例如，可以将活动日志警报规则配置为在事件启动时以及在已完成（成功或失败）时，通过不筛选事件状态字段来激发。 

    若要检查这些操作或通知是否来自不同警报，请检查警报详细信息，如警报时间戳以及警报 id 或相关 id。或者，在门户中检查触发的警报的列表。 如果是这种情况，则需要调整警报规则逻辑或配置警报源。 

1. **此操作是否在多个操作组中重复？** 

    触发警报时，将单独处理其每个操作组。 因此，如果某个操作（如电子邮件地址）出现在多个触发的操作组中，则会对每个操作组调用一次操作。 

    若要检查触发了哪些操作组，请检查 "警报历史记录" 选项卡。您将看到在警报规则中定义的操作组以及通过操作规则添加到警报的操作组： 

    ![在多个操作组中重复的操作](media/alerts-troubleshoot/action-repeated-multi-action-groups.png)

## <a name="action-or-notification-has-an-unexpected-content"></a>操作或通知具有意外内容

如果收到警报，但相信其某些字段缺失或不正确，请执行以下步骤： 

1. **是否为操作选择了正确的格式？** 

    每个操作类型（电子邮件、webhook 等）都有两种格式–默认格式、旧格式和[较新的通用架构格式](alerts-common-schema.md)。 创建操作组时，请指定每个操作所需的格式–操作组中的不同操作可能具有不同的格式。 

    例如，对于 webhook 操作： 

    ![webhook 操作架构选项](media/alerts-troubleshoot/webhook.png)

    检查在操作级别指定的格式是否与预期的相同。 例如，你可能已开发用于响应警报（webhook、函数、逻辑应用等）的代码，这些代码应为一种格式，但稍后在操作中或其他人指定了不同的格式。  

    此外，请检查[活动日志警报](activity-log-alerts-webhook.md)的负载格式（JSON），查看[是否有用于](alerts-log-webhook.md)[指标警报](alerts-metric-near-real-time.md#payload-schema)的事件日志警报（适用于 "Application Insights" 和 "log analytics"）、"[常见警报架构](alerts-common-schema-definitions.md)" 和 "弃用的[经典指标警报](alerts-webhooks.md)"。

 
1. **活动日志警报：活动日志中是否提供了信息？** 

    [活动日志警报](activity-log-alerts.md)是基于写入到 Azure 活动日志的事件的警报，如有关创建、更新或删除 azure 资源、服务运行状况和资源运行状况事件，或者从 azure Advisor 和 azure 策略中发现的事件。 如果收到基于活动日志的警报，但所需的某些字段缺失或不正确，请首先检查活动日志本身中的事件。 如果 Azure 资源未写入正在其活动日志事件中查找的字段，则这些字段将不会包含在相应的警报中。 

## <a name="action-rule-is-not-working-as-expected"></a>操作规则未按预期方式工作 

如果你可以在门户中看到触发的警报，但相关操作规则没有按预期方式工作，请执行以下步骤： 

1. **操作规则是否已启用？** 

    检查 "操作规则状态" 列以验证是否已启用相关操作角色。 

    ![图形](media/alerts-troubleshoot/action-rule-status.png) 

    如果未启用，则可以通过选择操作规则并单击 "启用" 来启用它。 

1. **它是否为服务运行状况警报？** 

    服务运行状况警报（监视器服务 = "服务运行状况"）不受操作规则影响。 

1. **操作规则对你的警报是否起作用？** 

    通过在门户中单击触发的警报，查看操作规则是否已处理警报，并查看 "历史记录" 选项卡。

    下面是禁止显示所有操作组的操作规则的示例： 
 
     ![警报操作规则禁止显示历史记录](media/alerts-troubleshoot/history-action-rule.png)

    下面是添加另一个操作组的操作规则的示例：

    ![在多个操作组中重复的操作](media/alerts-troubleshoot/action-repeated-multi-action-groups.png)
 

1. **操作规则的作用域和筛选器是否与触发的警报相符？** 

    如果你认为操作规则应该已激发但不应触发，但它不应被激发但确实如此，请仔细检查操作规则作用域和筛选条件与触发的警报的属性。 


## <a name="how-to-find-the-alert-id-of-a-fired-alert"></a>如何查找触发的警报的警报 id

当打开有关特定触发的警报的事例时（例如，如果你未收到其电子邮件通知），你将需要提供警报 id。 

若要找到它，请执行以下步骤：

1. 在 Azure 门户中，导航到触发的警报的列表，并找到该特定警报。 您可以使用筛选器来帮助您找到它。 

1. 单击警报以打开警报详细信息。 

1. 在第一个选项卡（"摘要" 选项卡）的警报字段中向下滚动，直到找到它并复制它。 该字段还包括可以使用的 "复制到剪贴板" 帮助器按钮。  

    ![查找警报 id](media/alerts-troubleshoot/get-alert-id.png)

## <a name="problem-creating-updating-or-deleting-action-rules-in-the-azure-portal"></a>在 Azure 门户中创建、更新或删除操作规则时出现问题

如果尝试创建、更新或删除[操作规则](alerts-action-rules.md)时收到错误，请执行以下步骤： 

1. **您是否收到了权限错误？**  

    应具有 " [监视参与者" 内置角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor)，或与操作规则和警报相关的特定权限。

1. **验证了操作规则参数吗？**  

    检查[操作规则文档](alerts-action-rules.md)或[操作规则 PowerShell AzActionRule](https://docs.microsoft.com/powershell/module/az.alertsmanagement/Set-AzActionRule?view=azps-3.5.0)命令。 


## <a name="next-steps"></a>后续步骤
- 如果使用日志警报，还应参阅[疑难解答日志警报](alert-log-troubleshoot.md)。
- 返回[Azure 门户](https://portal.azure.com)，检查以上指导是否已解决问题 
