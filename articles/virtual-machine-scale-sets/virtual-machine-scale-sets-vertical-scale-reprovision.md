---
title: 垂直缩放 Azure 虚拟机规模集
description: 如何使用 Azure 自动化垂直缩放虚拟机以响应监视警报
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 04/18/2019
ms.reviewer: avverma
ms.custom: avverma
ms.openlocfilehash: 69c613de02b9601966cae2d36c13428ca6c7becc
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83120991"
---
# <a name="vertical-autoscale-with-virtual-machine-scale-sets"></a>使用虚拟机规模集垂直自动缩放

本文介绍如何使用或不使用重新设置对 Azure [虚拟机规模集](https://azure.microsoft.com/services/virtual-machine-scale-sets/)进行垂直缩放。 

垂直缩放，也称为*纵向扩展*和*纵向缩减*，即增大或减小虚拟机 (VM) 大小，以响应工作负荷。 将此行为与[水平缩放](virtual-machine-scale-sets-autoscale-overview.md)（也称为扩大** 和缩小**，其中 VM 数目的更改取决于工作负荷）进行比较。

重新设置意味着删除现有 VM 并将其替换为新的 VM。 增加或减少虚拟机规模集中 VM 的大小时，某些情况下需要调整现有 VM 的大小并保留数据，而在其他情况下则需要部署新大小的新 VM。 本文档介绍这两种情况。

以下情况下，垂直缩放非常有用：

* 在虚拟机上构建的服务使用不足（例如周末）。 减小 VM 的大小可以减少每月成本。
* 增加 VM 的大小以处理更大的需求，而无需创建其他 VM。

可将垂直缩放设置为基于指标从虚拟机规模集触发警报。 警报被激活时，将开启 Webhook，此 Webhook 将触发可对规模集进行增加或减少的 Runbook。 可通过以下步骤配置垂直缩放：

1. 使用运行时功能创建 Azure 自动化帐户。
2. 将虚拟机规模集的 Azure 自动化垂直缩放 Runbook 导入订阅中。
3. 将 Webhook 添加到 Runbook。
4. 将警报添加到使用 Webhook 通知的虚拟机规模集。

> [!NOTE]
> 由于第一个虚拟机的大小有限制，它可以调整到的大小可能会由于当前虚拟机部署到的群集的其他大小而受到限制。 在本文中使用的已发布自动化 Runbook 中，我们将遵循这种限制，只在以下 VM 大小对的范围内进行缩放。 这意味着，Standard_D1v2 虚拟机不会突然间扩展到 Standard_G5，或者突然间缩减到 Basic_A0。 还不支持约束的虚拟机大小增加/减少。 可以选择在以下大小对之间缩放：
> 
> | VM 大小缩放对 |  |
> | --- | --- |
> | Basic_A0 |Basic_A4 |
> | Standard_A0 |Standard_A4 |
> | Standard_A5 |Standard_A7 |
> | Standard_A8 |Standard_A9 |
> | Standard_A10 |Standard_A11 |
> | Standard_A1_v2 |Standard_A8_v2 |
> | Standard_A2m_v2 |Standard_A8m_v2  |
> | Standard_B1s |Standard_B2s |
> | Standard_B1ms |Standard_B8ms |
> | Standard_D1 |Standard_D4 |
> | Standard_D11 |Standard_D14 |
> | Standard_DS1 |Standard_DS4 |
> | Standard_DS11 |Standard_DS14 |
> | Standard_D1_v2 |Standard_D5_v2 |
> | Standard_D11_v2 |Standard_D14_v2 |
> | Standard_DS1_v2 |Standard_DS5_v2 |
> | Standard_DS11_v2 |Standard_DS14_v2 |
> | Standard_D2_v3 |Standard_D64_v3 |
> | Standard_D2s_v3 |Standard_D64s_v3 |
> | Standard_DC2s |Standard_DC4s |
> | Standard_E2_v3 |Standard_E64_v3 |
> | Standard_E2s_v3 |Standard_E64s_v3 |
> | Standard_F1 |Standard_F16 |
> | Standard_F1s |Standard_F16s |
> | Standard_F2sv2 |Standard_F72sv2 |
> | Standard_G1 |Standard_G5 |
> | Standard_GS1 |Standard_GS5 |
> | Standard_H8 |Standard_H16 |
> | Standard_H8m |Standard_H16m |
> | Standard_L4s |Standard_L32s |
> | Standard_L8s_v2 |Standard_L80s_v2 |
> | Standard_M8ms  |Standard_M128ms |
> | Standard_M32ls  |Standard_M64ls |
> | Standard_M64s  |Standard_M128s |
> | Standard_M64  |Standard_M128 |
> | Standard_M64m  |Standard_M128m |
> | Standard_NC6 |Standard_NC24 |
> | Standard_NC6s_v2 |Standard_NC24s_v2 |
> | Standard_NC6s_v3 |Standard_NC24s_v3 |
> | Standard_ND6s |Standard_ND24s |
> | Standard_NV6 |Standard_NV24 |
> | Standard_NV6s_v2 |Standard_NV24s_v2 |
> | Standard_NV12s_v3 |Standard_NV48s_v3 |
> 

## <a name="create-an-azure-automation-account-with-run-as-capability"></a>使用运行时功能创建 Azure 自动化帐户
需要做的第一件事是创建一个 Azure 自动化帐户来托管用于缩放虚拟机规模集实例的 Runbook。 最近，[Azure 自动化](https://azure.microsoft.com/services/automation/)引入了“运行方式帐户”功能，使用该功能可以代表用户设置服务主体来自动运行 Runbook。 有关详细信息，请参见:

* [使用 Azure 运行方式帐户进行 Runbook 身份验证](../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>将 Azure 自动化垂直缩放 Runbook 导入到订阅中

垂直缩放虚拟机规模集所需的 Runbook 已在 Azure 自动化 Runbook 库中发布。 要将其导入到订阅中，请按照这篇文章中的步骤进行操作：

* [Azure 自动化的 Runbook 和模块库](../automation/automation-runbook-gallery.md)

从 Runbook 菜单中选择“浏览库”选项：

![要导入的 Runbook][runbooks]

显示需要导入的 Runbook。 根据是否希望垂直缩放（使用或不使用重新设置），选择 Runbook：

![Runbook 库][gallery]

## <a name="add-a-webhook-to-your-runbook"></a>将 Webhook 添加到 Runbook

导入 runbook 后，将 webhook 添加到 runbook，以便虚拟机规模集中的警报可以触发它。 本文对有关如何为 Runbook 创建 Webhook 的详细信息进行了描述：

* [Azure 自动化 Webhook](../automation/automation-webhooks.md)

> [!NOTE]
> 关闭 Webhook 对话框之前，请务必复制 Webhook URI，因为在下一部分中将要用到此地址。
> 
> 

## <a name="add-an-alert-to-your-virtual-machine-scale-set"></a>将警报添加到虚拟机规模集

下面是 PowerShell 脚本，演示如何将警报添加到虚拟机规模集。 请参阅以下文章以获取可触发警报的指标名称：[Azure 监视器自动缩放常用指标](../azure-monitor/platform/autoscale-common-metrics.md)。

```powershell
$actionEmail = New-AzAlertRuleEmail -CustomEmail user@contoso.com
$actionWebhook = New-AzAlertRuleWebhook -ServiceUri <uri-of-the-webhook>
$threshold = <value-of-the-threshold>
$rg = <resource-group-name>
$id = <resource-id-to-add-the-alert-to>
$location = <location-of-the-resource>
$alertName = <name-of-the-resource>
$metricName = <metric-to-fire-the-alert-on>
$timeWindow = <time-window-in-hh:mm:ss-format>
$condition = <condition-for-the-threshold> # Other valid values are LessThanOrEqual, GreaterThan, GreaterThanOrEqual
$description = <description-for-the-alert>

Add-AzMetricAlertRule  -Name  $alertName `
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

* [Azure Monitor PowerShell 快速入门示例](../azure-monitor/platform/powershell-quickstart-samples.md)
* [Azure Monitor 跨平台 CLI 快速入门示例](../azure-monitor/platform/cli-samples.md)

## <a name="summary"></a>摘要

本文对简单的垂直缩放示例进行了介绍。 借助这些构建基块 - 自动化帐户、Runbook、Webhook、警报，可以使用一组自定义操作连接各种事件。

[runbooks]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks.png
[gallery]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks-gallery.png
