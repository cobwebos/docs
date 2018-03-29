---
title: 从 Log Analytics 警报调用 Azure 自动化 Runbook
description: 本文概述如何从 Operations Management Suite 中的 Log Analytics 警报调用自动化 Runbook。
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 3f95d6b9385b252bce05f19b38ae38f11e88a88c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="call-an-azure-automation-runbook-from-a-log-analytics-alert"></a>从 Log Analytics 警报调用 Azure 自动化 Runbook

可以在 Azure Log Analytics 中将警报配置为当结果符合条件时创建警报记录。 然后，该警报可以自动运行一个 Azure 自动化 Runbook，尝试自动修正问题。 

例如，出现警报可能表示处理器利用率陡升且持续很长时间， 也可能表示某个对业务应用程序的功能很关键的应用程序进程发生故障。 然后，某个 Runbook 会将相应的事件写入 Windows 事件日志。  

在警报配置中有两个调用 Runbook 的选项：

* 使用 Webhook。
   * 如果 Operations Management Suite 工作区未链接到自动化帐户，则此选项是唯一可用的选项。
   * 如果已有一个链接到 Operations Management Suite 工作区的自动化帐户，此选项仍然可用。  

* 直接选择 Runbook。
   * 仅当 Operations Management Suite 工作区已链接到自动化帐户时，此选项才可用。

## <a name="calling-a-runbook-by-using-a-webhook"></a>使用 Webhook 调用 Runbook

可以使用 Webhook 在 Azure 自动化中通过单个 HTTP 请求启动特定的 Runbook。 在将 [Log Analytics 警报](../log-analytics/log-analytics-alerts.md#alert-rules)配置为使用 Webhook 以警报操作的形式调用 Runbook 之前，需要针对通过此方法调用的 Runbook [创建一个 Webhook](automation-webhooks.md#creating-a-webhook)。 别忘了要记下 Webhook URL，以便在配置警报规则时引用。   

## <a name="calling-a-runbook-directly"></a>直接调用 Runbook

可以在 Operations Management Suite 工作区中安装并配置自动化和控制产品/服务。 在配置警报的 Runbook 操作选项时，可以从“选择 Runbook”下拉列表中查看所有 Runbook，并选择需要在响应警报时运行的特定 Runbook。 选定的 Runbook 可以在 Azure 工作区内运行，也可以在混合 Runbook 辅助角色上运行。 

在你使用 Runbook 选项创建警报后，系统将为 Runbook 创建 Webhook。 转到自动化帐户并打开选定 Runbook 的 Webhook 窗格，即可看到该 Webhook。 

删除警报时，不会删除 Webhook。 这不是问题。 真正的问题是，该 Webhook 会变成一个最终需要手动删除的孤立项，而手动删除的目的是保持自动化帐户的井然有序。  

## <a name="characteristics-of-a-runbook"></a>Runbook 的特征

从 Log Analytics 警报调用 Runbook 的两种方法具有某些特征，在配置警报规则之前，需要了解这些特征。 

在名为 **SearchResult** 的单个属性中，警报数据采用 JSON 格式。 此格式适用于使用标准有效负载的 runbook 和 webhook 操作。 对于使用自定义有效负载（包括 **RequestBody** 中的 **IncludeSearchResults:True**）的 Webhook 操作，此属性为 **SearchResults**。

必须创建一个名为 **WebhookData**、类型为**对象**的 Runbook 输入参数。 该参数可以是必需的，也可以是可选的。 警报会使用此输入参数将搜索结果传递给 Runbook。

```powershell
param  
    (  
    [Parameter (Mandatory=$true)]  
    [object] $WebhookData  
    )
```
还必须使用代码将 **WebhookData** 转换为 PowerShell 对象。

```powershell
$SearchResult = (ConvertFrom-Json $WebhookData.RequestBody).SearchResult.value
```

**$SearchResult** 是一个对象数组。 每个对象包含一些字段，字段值取自一条搜索结果。


## <a name="example-walkthrough"></a>示例演练

以下有关图形 Runbook 的示例演示了此进程的工作原理。 它启动 Windows 服务。

![用于启动 Windows 服务的图形 Runbook](media/automation-invoke-runbook-from-omsla-alert/automation-runbook-restartservice.png)

此 Runbook 有一个**对象**类型的名为 **WebhookData** 的输入参数。 其中的 Webhook 数据是从警报传递过来的，警报中包含 **SearchResult**。

![Runbook 输入参数](media/automation-invoke-runbook-from-omsla-alert/automation-runbook-restartservice-inputparameter.png)

我们已在 Log Analytics 中为此示例创建了两个自定义字段：**SvcDisplayName_CF** 和 **SvcState_CF**。 这些字段从已写入系统事件日志的事件提取服务显示名称和服务状态（即“正在运行”或“已停止”）。 然后，我们使用以下搜索查询创建警报规则，以便 Windows 系统上的“打印后台处理程序”服务停止时，可以检测到这种情况。

`Type=Event SvcDisplayName_CF="Print Spooler" SvcState_CF="stopped"` 

检测的服务可以是感兴趣的任何服务。 在本示例中，我们需要引用 Windows OS 中包含的一个预先存在的服务。 警报操作配置为执行本示例中使用的 Runbook，并让其在目标系统中启用的混合 Runbook 辅助角色上运行。   

Runbook 代码活动“从 LA 获取服务名称”可将 JSON 格式的字符串转换成对象类型，并对 **SvcDisplayName_CF** 项进行筛选。 它会提取 Windows 服务的显示名称并将该值传递给下一活动，后者会在尝试重启服务之前验证服务是否处于“已停止”状态。 **SvcDisplayName_CF** 是在 Log Analytics 中创建的用于演示本示例的[自定义字段](../log-analytics/log-analytics-custom-fields.md)。

```powershell
$SearchResult = (ConvertFrom-Json $WebhookData.RequestBody).SearchResult.value
$SearchResult.SvcDisplayName_CF  
```

当服务停止时，Log Analytics 中的警报规则会检测匹配项并触发 Runbook，然后将警报上下文发送给 Runbook。 Runbook 会尝试验证服务是否已停止。 如果服务已停止，Runbook 会尝试重启服务，并在验证其已正确启动之后显示结果。     

或者，如果尚未将自动化帐户链接到 Operations Management Suite 工作区，则可使用 Webhook 操作来配置警报规则。 Webhook 操作可触发 Runbook。 它还可以遵循此前提到的指南对 Runbook 进行配置，以便转换 JSON 格式的字符串并对 **SearchResult** 进行筛选。    

>[!NOTE]
> 如果工作区已升级到[新 Log Analytics 查询语言](../log-analytics/log-analytics-log-search-upgrade.md)，则 Webook 有效负载已更改。 [Azure Log Analytics REST API](https://aka.ms/loganalyticsapiresponse) 中提供了格式的详细信息。

## <a name="next-steps"></a>后续步骤

* 若要详细了解 Log Analytics 中的警报以及如何创建警报，请参阅 [Log Analytics 中的警报](../log-analytics/log-analytics-alerts.md)。

* 若要了解如何使用 Webhook 触发 Runbook，请参阅 [Azure 自动化 Webhook](automation-webhooks.md)。
