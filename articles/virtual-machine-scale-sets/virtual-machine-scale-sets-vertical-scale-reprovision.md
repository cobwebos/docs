---
title: "垂直缩放 Azure 虚拟机规模集 | Microsoft Docs"
description: "如何使用 Azure 自动化垂直缩放虚拟机以响应监视警报"
services: virtual-machine-scale-sets
documentationcenter: 
author: gbowerman
manager: madhana
editor: 
tags: azure-resource-manager
ms.assetid: 16b17421-6b8f-483e-8a84-26327c44e9d3
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 08/03/2016
ms.author: guybo
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: 4533b446f661568d0e70a23bb64880022038bb9a


---
# <a name="vertical-autoscale-with-virtual-machine-scale-sets"></a>使用虚拟机规模集垂直自动缩放
本文介绍如何使用或不使用重新设置对 Azure [虚拟机规模集](https://azure.microsoft.com/services/virtual-machine-scale-sets/)进行垂直缩放。 有关 VM 的垂直缩放（规模集中不存在），请参阅[使用 Azure 自动化垂直缩放 Azure 虚拟机](../virtual-machines/virtual-machines-windows-vertical-scaling-automation.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

垂直缩放，也称为增加和减少，即增加或减少虚拟机 (VM) 大小，以响应工作负荷。 将此与[水平缩放](virtual-machine-scale-sets-autoscale-overview.md)（也称为扩大和缩小，其中 VM 数目的更改取决于工作负荷）进行比较。

重新设置意味着删除现有 VM 并将其替换为新的 VM。 增加或减少 VM 规模集中 VM 的大小时，某些情况下您想要调整现有 VM 的大小并保留数据，尽管在其他情况下您需要部署新 VM 的新大小。 本文档介绍这两种情况。

以下情况下，垂直缩放非常有用：

* 在虚拟机上构建的服务使用不足（例如周末）。 减小 VM 的大小可以减少每月成本。
* 增加 VM 的大小以处理更大的需求，而无需创建其他 VM。

可将垂直缩放设置为基于 VM 规模集的警报根据指标触发。 警报被激活时，将开启 Webhook，此 Webhook 将触发可对规模集进行增加或减少的 Runbook。 可通过以下步骤配置垂直缩放：

1. 使用运行时功能创建 Azure 自动化帐户。
2. 将 VM 规模集的 Azure 自动化垂直缩放 Runbook 导入您的订阅中。
3. 将 Webhook 添加到 Runbook。
4. 将警报添加到使用 Webhook 通知的 VM 规模集。

> [!NOTE]
> 垂直自动缩放仅在 VM 大小的一定范围内发生。 在决定从一个规模缩放到另一个规模之前，比较每个大小的规范（数字越大并不表示 VM 大小越大）。 可以选择在以下大小对之间缩放：
> 
> | VM 大小缩放对 |  |
> | --- | --- |
> | Standard_A0 |Standard_A11 |
> | Standard_D1 |Standard_D14 |
> | Standard_DS1 |Standard_DS14 |
> | Standard_D1v2 |Standard_D15v2 |
> | Standard_G1 |Standard_G5 |
> | Standard_GS1 |Standard_GS5 |
> 
> 

## <a name="create-an-azure-automation-account-with-run-as-capability"></a>使用运行时功能创建 Azure 自动化帐户
要做的第一件事就是创建一个 Azure 自动化帐户来托管用于缩放 VM 缩放集实例的 Runbook。 最近，[Azure 自动化](https://azure.microsoft.com/services/automation/)引入了“运行方式帐户”功能，使用该功能可以很轻松地代表用户设置服务主体来自动运行 Runbook。 可以在以下文章中阅读有关此功能的详细信息：

* [Authenticate Runbooks with Azure Run As account（使用 Azure 运行方式帐户进行 Runbook 身份验证）](../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>将 Azure 自动化垂直缩放 Runbook 导入到订阅中
需要垂直缩放 VM 规模集的 Runbook 已在 Azure 自动化 Runbook 库中发布。 若要将其导入到订阅中，请按照这篇文章中的步骤进行操作：

* [Azure 自动化的 Runbook 和模块库](../automation/automation-runbook-gallery.md)

从 Runbook 菜单中选择“浏览库”选项：

![要导入的 Runbook][runbooks]

显示需要导入的 Runbook。 根据您是否希望垂直缩放（使用或不使用重新设置），选择 Runbook：

![Runbook 库][gallery]

## <a name="add-a-webhook-to-your-runbook"></a>将 Webhook 添加到 Runbook
导入 Runbook 后，需在 Runbook 中添加一个 Webhook，以便 VM 规模集发出的警报可以触发 Runbook。 本文对有关如何为 Runbook 创建 Webhook 的详细信息进行了描述：

* [Azure 自动化 Webhook](../automation/automation-webhooks.md)

> [!NOTE]
> 在关闭 Webhook 对话框之前，请务必复制 Webhook URI，因为在下一部分将要用到它。
> 
> 

## <a name="add-an-alert-to-your-vm-scale-set"></a>添加针对 VM 规模集的警报
下面是 PowerShell 脚本，对如何将警报添加到 VM 规模集进行演示。 请参阅以下文章以获取可触发警报的指标名称：[Azure 监视器自动缩放常用指标](../monitoring-and-diagnostics/insights-autoscale-common-metrics.md)。

```
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail user@contoso.com
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri <uri-of-the-webhook>
$threshold = <value-of-the-threshold>
$rg = <resource-group-name>
$id = <resource-id-to-add-the-alert-to>
$location = <location-of-the-resource>
$alertName = <name-of-the-resource>
$metricName = <metric-to-fire-the-alert-on>
$timeWindow = <time-window-in-hh:mm:ss-format>
$condition = <condition-for-the-threshold> # Other valid values are LessThanOrEqual, GreaterThan, GreaterThanOrEqual
$description = <description-for-the-alert>

Add-AzureRmMetricAlertRule  -Name  $alertName `
                            -Location  $location `
                            -ResourceGroup $rg `
                            -TargetResourceId $id `
                            -MetricName $metricName `
                            -Operator  $condition `
                            -Threshold $threshold `
                            -WindowSize  $timeWindow `
                            -TimeAggregationOperator Average `
                            -Actions $actionEmail, $actionWebhook `
                            -Description $description
```

> [!NOTE]
> 建议为警报配置合理的时间范围，以便避免过于频繁地触发垂直缩放和任何关联的服务中断。 请考虑采用至少 20-30 分钟或更长的时间范围。 如果需要避免任何中断，请考虑采用水平缩放。
> 
> 

有关如何创建警报的详细信息，请参阅以下文章：

* [Azure 监视器 PowerShell 快速入门示例](../monitoring-and-diagnostics/insights-powershell-samples.md)
* [Azure 监视器跨平台 CLI 快速入门示例](../monitoring-and-diagnostics/insights-cli-samples.md)

## <a name="summary"></a>摘要
本文对简单的垂直缩放示例进行了介绍。 借助这些构建基块 - 自动化帐户、Runbook、Webhook、警报，您可以使用一组自定义操作连接各种事件。

[Runbook]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks.png
[库]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks-gallery.png



<!--HONumber=Nov16_HO3-->


