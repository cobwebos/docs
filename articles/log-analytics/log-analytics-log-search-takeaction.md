---
title: Log Analytics 中由用户启动的 Azure 自动化 Runbook 操作 | Microsoft Docs
description: 本文介绍了如何按需从 Log Analytics 搜索结果中运行自动化 runbook。
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: log-analytics
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/04/2017
ms.author: magoedte
ms.component: na
ms.openlocfilehash: 48e184206d9f1cf5b14c2e18b4ca1ed260d70161
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37130210"
---
# <a name="take-action-with-an-automation-runbook-from-a-log-analytics-log-search-result"></a>从 Log Analytics 日志搜索结果中通过自动化 Runbook 采取操作

从 Azure Log Analytics 中的日志搜索结果中，现在可以选择“采取操作”来运行自动化 runbook。  可以使用 runbook 来更正问题或采取其他操作，例如，收集故障排除信息、发送电子邮件，或创建服务请求。 

## <a name="components-and-features-used"></a>所使用的组件和功能
* [Azure 自动化帐户](../automation/automation-offering-get-started.md)
* [Log Analytics 工作区](../log-analytics/log-analytics-overview.md)

## <a name="to-initiate-runbook-from-log-search"></a>从日志搜索中启动 runbook

要从日志搜索结果中对事件采取操作或启动 runbook，首先需要创建日志搜索，从结果中，可以按需调用 runbook。  这可以通过 [Azure 门户](../log-analytics/log-analytics-log-search-new.md)中的日志搜索功能来实现。  在此示例中，我们从 Azure 门户中执行日志搜索，对此功能进行了基本演示。

1. 在 Azure 门户中，单击“所有服务”，并选择“Log Analytics”。  
2. 选择 Log Analytics 工作区。
3. 在工作区中，选择“日志搜索”。  
4. 在“日志搜索”页中，执行日志搜索。  
5. 从日志搜索结果中，单击某个字段左侧的省略号，从弹出窗口中选择“对其采取操作”。<br><br> ![从搜索结果中选择“采取操作”](./media/log-analytics-log-search-takeaction/log-search-takeaction-menuoption.png) 
6. 选择“运行 runbook”并选择要运行的 runbook。  可以在链接到 Log Analytics 工作区的自动化帐户中选择任何 runbook。  注意以下事项：

    * Runbook 按标记进行组织
    * 可以直接从搜索结果的字段中选择Runbook 输入参数值。  一个下拉列表会显示可从中进行选择的结果中可用的所有字段。  
    * 可以选择在有问题的计算机上安装的[混合 Runbook 辅助角色](../automation/automation-hybrid-runbook-worker.md)中运行 Runbook（如果具有对应的仅包含此计算机作为成员的混合 Runbook 辅助角色组）。  如果混合辅助角色组的名称与日志搜索结果中的计算机名称匹配，则会自动选择该组。    

6. 单击“运行”后，“runbook 作业”页将打开，可以在其中查看作业状态。   

如果选择了已配置为[从 Log Analytics 警报调用](../automation/automation-invoke-runbook-from-omsla-alert.md)的 runbook，则它具有一个名为 **WebhookData** 且类型为 **Object** 的输入参数。  如果该输入参数是必需的，则需要将搜索结果传递到 runbook，以便它可以将 JSON 格式的字符串转换为对象类型，从而允许筛选可在 runbook 活动中引用的特定项。  可以通过从下拉列表中选择“搜索结果(对象)”来执行此操作。<br><br> ![为 runbook 参数选择 Webhook 数据对象](media/log-analytics-log-search-takeaction/select-runbook-and-properties.png)   
    
## <a name="next-steps"></a>后续步骤

* 查看 [Log Analytics 日志搜索引用](log-analytics-search-reference.md)以查看 Log Analytics 中所有可用的搜索字段和 Facet。
* 若要了解如何自动调用自动化 runbook ，请查看[从 Log Analytics 警报调用 Azure 自动化 runbook](../automation/automation-invoke-runbook-from-omsla-alert.md)。  