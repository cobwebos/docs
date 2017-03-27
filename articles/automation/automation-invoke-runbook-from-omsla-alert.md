---
title: "从 Log Analytics 警报调用 Azure 自动化 Runbook | Microsoft 文档"
description: "本文概述如何从 Microsoft OMS Log Analytics 警报调用自动化 Runbook。"
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/31/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 8460ed6be3e922fb85f46982662d44eed21dda7c
ms.lasthandoff: 03/18/2017

---

# <a name="calling-an-azure-automation-runbook-from-an-oms-log-analytics-alert"></a>从 OMS Log Analytics 警报调用 Azure 自动化 Runbook

在 Log Analytics 中配置一个用于创建警报记录的警报后，如果结果与特定的条件匹配（例如，长时间出现处理器利用率峰值，或者对业务应用程序的功能至关重要的特定应用程序进程失败并在 Windows 事件日志中写入了相应的事件），该警报可自动运行某个自动化 Runbook，以尝试自动修正问题。  

配置警报时，可以使用两个选项来调用 Runbook。  具体而言：

1. 使用 Webhook。
   * 如果 OMS 工作区未链接到自动化帐户，则此选项是唯一可用的选项。
   * 如果已有一个链接到 OMS 工作区的自动化帐户，此选项仍然可用。  

2. 直接选择 Runbook。
   * 仅当 OMS 工作区已链接到自动化帐户时，此选项才可用。  

## <a name="calling-a-runbook-using-a-webhook"></a>使用 Webhook 调用 Runbook

使用 Webhook 可在 Azure 自动化中通过单个 HTTP 请求启动特定的 Runbook。  在将 [Log Analytics 警报](../log-analytics/log-analytics-alerts.md#creating-alert-rules)配置为使用 Webhook 以警报操作的形式调用 Runbook 之前，首先需要针对使用此方法调用的 Runbook 创建一个 Webhook。  请查看并遵循[创建 Webhook](automation-webhooks.md#creating-a-webhook) 一文中的步骤，别忘了要记下 Webhook URL，以便在配置警报规则时引用。   

## <a name="calling-a-runbook-directly"></a>直接调用 Runbook

如果已在 OMS 工作区中安装并配置自动化与控制产品，则在配置警报的 Runbook 操作选项时，可以从“选择 Runbook”下拉列表中查看所有 Runbook，然后选择在响应警报时要运行的特定 Runbook。  选定的 Runbook 可以在 Azure 云中的某个工作区内运行，也可以在混合 Runbook 辅助角色上运行。  使用 Runbook 选项创建警报时，将为 Runbook 创建 Webhook。  转到自动化帐户并导航到选定 Runbook 的 Webhook 边栏选项卡，即可看到该 Webhook。  删除警报时不会删除 Webhook，但用户可以手动将它删除。  不删除 Webhook 并不会造成问题，它只是以孤立的项存在，但为了保持自动化帐户的井然有序，最终还是需要将它删除。  

## <a name="characteristics-of-a-runbook-for-both-options"></a>Runbook 的特征（适用于上述两个选项）

从 Log Analytics 警报调用 Runbook 的两种方法具有不同的行为特征，在配置警报规则之前，需要理解这些特征。  

* 必须创建一个名为 **WebhookData**、类型为 **Object** 的 Runbook 输入参数。  该参数可以是必需的，也可以是可选的。  警报会使用此输入参数将搜索结果传递给 Runbook。

        param  
         (  
          [Parameter (Mandatory=$true)]  
          [object] $WebhookData  
         )

*  必须使用代码将 WebhookData 转换为 PowerShell 对象。

    `$SearchResults = (ConvertFrom-Json $WebhookData.RequestBody).SearchResults.value`

    *$SearchResults* 是一个对象数组；每个对象包含一些字段，字段值取自一条搜索结果

### <a name="webhookdata-inconsistencies-between-the-webhook-option-and-runbook-option"></a>Webhook 选项与 Runbook 选项之间的 WebhookData 不一致性

* 将警报配置为调用 Webhook 时，请输入针对 Runbook 创建的 Webhook URL，然后单击“测试 Webhook”按钮。  发送到 Runbook 的最终 WebhookData 不包含 *.SearchResult* 或 *.SearchResults*。

*  如果保存该警报，当警报触发和调用 Webhook 时，发送到 Runbook 的 WebhookData 会包含 *.SearchResult*。
* 如果创建警报并将其配置为调用 Runbook（这也会创建 Webhook），则警报触发时，会将 WebhookData 发送到包含 *.SearchResults* 的 Runbook。

因此在上面的代码示例中，如果警报调用 Webhook，则你需要获取 *.SearchResult*；如果警报直接调用 Runbook，则你需要获取 *.SearchResults*。

## <a name="example-walkthrough"></a>示例演练

本部分使用以下可以启动 Windows 服务的示例图形 Runbook 来演示上述操作。<br><br> ![启动 Windows 服务的图形 Runbook](media/automation-invoke-runbook-from-omsla-alert/automation-runbook-restartservice.png)<br>

该 Runbook 具有一个名为 **WebhookData**、类型为 **Object** 的输入参数，并包含从警报（该警报包含 *.SearchResults*）传递的 Webhook 数据。<br><br> ![Runbook 输入参数](media/automation-invoke-runbook-from-omsla-alert/automation-runbook-restartservice-inputparameter.png)<br>

对于本示例，我们在 Log Analytics 中创建了两个自定义字段：*SvcDisplayName_CF* 和*SvcState_CF*，用于从写入到系统事件日志的事件中提取服务显示名称和服务的状态（即 running 或 stopped）。  然后，使用以下搜索查询创建警报规则：`Type=Event SvcDisplayName_CF="Print Spooler" SvcState_CF="stopped"`，以便 Windows 系统上的“打印后台处理程序”服务停止时，可以检测到这种情况。  也可以检测任何所需的服务，但在本示例中，我们探讨的是 Windows OS 中包含的一个预先存在的服务。  警报操作配置为执行本示例中使用的 Runbook，并在目标系统中启用的混合 Runbook 辅助角色上运行。   

Runbook 代码活动“从 LA 获取服务名称”会将 JSON 格式的字符串转换为 object 类型，然后根据项 *SvcDisplayName_CF* 进行筛选，以提取 Windows 服务的显示名称并将此信息传递给下一个活动，后者在尝试重新启动服务之前会验证该服务是否已停止。  *SvcDisplayName_CF* 是在 Log Analytics 中创建的用于演示本示例的[自定义字段](../log-analytics/log-analytics-custom-fields.md)。

    $SearchResults = (ConvertFrom-Json $WebhookData.RequestBody).SearchResults.value
    $SearchResults.SvcDisplayName_CF  

当服务停止时，Log Analytics 中的警报规则将检测匹配项并触发 Runbook，然后将警报上下文发送给 Runbook。 Runbook 将执行操作来验证服务是否已停止，如果已停止，则尝试重新启动服务，然后验证服务是否已正常启动，并输出结果。     

如果未将自动化帐户链接到 OMS 工作区，可遵循前面所述的指导，在警报规则中配置一个 Webhook 操作来触发 Runbook，将 Runbook 配置为转换 JSON 格式的字符串并根据 *.SearchResult* 进行筛选。    

## <a name="next-steps"></a>后续步骤

* 若要详细了解 Log Analytics 中的警报以及如何创建警报，请参阅 [Log Analytics 中的警报](../log-analytics/log-analytics-alerts.md)。

* 若要了解如何使用 Webhook 触发 Runbook，请参阅 [Azure 自动化 Webhook](automation-webhooks.md)。

