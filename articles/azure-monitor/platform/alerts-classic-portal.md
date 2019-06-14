---
title: 使用 Azure Monitor 创建、查看和管理经典指标警报
description: 了解如何使用 Azure 门户、CLI 或 Powershell 来创建、查看和管理经典指标警报规则。
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: snmuvva
ms.openlocfilehash: 4a225dbc8e84d65a6ea25f63627599e5bb7d2ced
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66129792"
---
# <a name="create-view-and-manage-classic-metric-alerts-using-azure-monitor"></a>使用 Azure Monitor 创建、查看和管理经典指标警报

Azure Monitor 中的经典指标警报提供了一种在指标超出阈值时获得通知的方法。 经典指标警报是一个较旧的功能，它仅可针对非维度指标发出警报。 有一个称为“指标警报”的较新功能，它具有比经典指标警报改进的功能。 可以在[指标警报概述](../../azure-monitor/platform/alerts-metric-overview.md)中了解新的指标警报功能的详细信息。 在本文中，我们将介绍如何通过 Azure 门户、Azure CLI 和 Powershell 创建、查看和管理经典指标警报规则。

## <a name="with-azure-portal"></a>使用 Azure 门户

1. 在[门户](https://portal.azure.com/)中，找到要监视的资源，然后选择它。

2. 在“监视”部分中，选择“警报(经典)”   。 对于不同的资源，文本和图标可能会略有不同。 如果在此处未找到“警报(经典)”，则它可能位于“警报”或“警报规则”中    。

    ![监视](media/alerts-classic-portal/AlertRulesButton.png)

3. 选择“添加指标警报(经典)”  命令，并填写字段。

    ![添加警报](media/alerts-classic-portal/AddAlertOnlyParamsPage.png)

4. **命名**警报规则。 然后选择也在通知电子邮件中显示的“说明”  。

5. 选择要监视的指标  。 然后为该指标选择“条件”和“阈值”值   。 还选择触发警报前指标规则必须满足的时间段。  例如，如果使用时间段“过去 5 分钟”，且警报针对 CPU 高于 80% 的情况，则 CPU 持续高于 80% 达到 5 分钟时将触发警报。 第一次触发后，CPU 5 分钟内持续低于 80% 时会再次触发警报。 CPU 指标度量每分钟进行一次。

6. 如果希望在触发警报时管理员和共同管理员收到电子邮件通知，则选择“向所有者发送电子邮件...”  。

7. 如果希望在触发警报时向其他电子邮件发送通知，请将其添加到“其他管理员电子邮件”字段  。 使用采用以下格式的分号分隔多个电子邮件：*电子邮件\@contoso.com;email2\@contoso.com*

8. 如果希望在触发警报时调用有效的 URI，请将其放入“Webhook”  字段中。

9. 如果使用 Azure 自动化时，则可选择在触发警报时要运行的 Runbook。

10. 选择“确定”  以创建警报。

几分钟后，警报将处于活动状态，并按前面所述进行触发。

创建警报后，可以选择它并执行以下任务之一：

* 查看显示指标阈值和前一天实际值的关系图。
* 编辑或删除其。
* 如果想要暂时停止或恢复接收该警报的通知，可**禁用**或**启用**它。

## <a name="with-azure-cli"></a>使用 Azure CLI

前面几个部分介绍了如何使用 Azure 门户创建、查看和管理指标警报规则。 本部分将介绍如何使用跨平台 [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest) 实现相同的结果。 开始使用 Azure CLI 的最快方式是通过 [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)。

### <a name="get-all-classic-metric-alert-rules-in-a-resource-group"></a>获取某个资源组中的所有经典指标警报规则

```azurecli
az monitor alert list --resource-group <group name>
```

### <a name="see-details-of-a-particular-classic-metric-alert-rule"></a>查看特定经典指标警报规则的详细信息

```azurecli
az monitor alert show --resource-group <group name> --name <alert name>
```

### <a name="create-a-classic-metric-alert-rule"></a>创建经典指标警报规则

```azurecli
az monitor alert create --name <alert name> --resource-group <group name> \
    --action email <email1 email2 ...> \
    --action webhook <URI> \
    --target <target object ID> \
    --condition "<METRIC> {>,>=,<,<=} <THRESHOLD> {avg,min,max,total,last} ##h##m##s"
```

### <a name="delete-a-classic-metric-alert-rule"></a>删除经典指标警报规则

```azurecli
az monitor alert delete --name <alert name> --resource-group <group name>
```

## <a name="with-powershell"></a>使用 PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

本部分展示了如何使用 PowerShell 命令创建、查看和管理经典指标警报。本文中的示例展示了如何将 Azure Monitor cmdlet 用于经典指标警报。

1. 如果尚未安装，请在计算机上安装要运行的 PowerShell。 有关详细信息，请参阅[如何安装和配置 PowerShell](/powershell/azure/overview)。 还可以在 [Azure 监视器 (Insights) Cmdlet](https://docs.microsoft.com/powershell/module/az.applicationinsights) 上查看 Azure 监视器 PowerShell cmdlet 的完整列表。

2. 首先，登录到 Azure 订阅。

    ```powershell
    Connect-AzAccount
    ```

3. 会出现登录界面。 登录帐户后，会出现 TenantID 和默认订阅 ID。 所有 Azure cmdlets 都在默认订阅的上下文中工作。 若要查看有权访问的订阅的列表，请使用以下命令：

    ```powershell
    Get-AzSubscription
    ```

4. 若要将工作环境更改为另一订阅，请使用以下命令：

    ```powershell
    Set-AzContext -SubscriptionId <subscriptionid>
    ```

5. 可以检索某个资源组中的所有经典指标警报规则：

    ```powershell
    Get-AzAlertRule -ResourceGroup montest
    ```

6. 可以查看经典指标警报规则的详细信息

    ```powershell
    Get-AzAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
    ```

7. 可以检索为目标资源设置的所有警报规则。 例如，虚拟机上的所有警报规则设置。

    ```powershell
    Get-AzAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
    ```

8. 无法再通过 PowerShell 创建经典警报规则。 若要创建警报规则，需要使用新的 [Add-AzMetricAlertRule](/powershell/module/az.monitor/add-azmetricalertrule) 命令。

## <a name="next-steps"></a>后续步骤

- [使用资源管理器模板创建经典指标警报](../../azure-monitor/platform/alerts-enable-template.md)。
- [使用 Webhook 让经典指标警报通知非 Azure 系统](../../azure-monitor/platform/alerts-webhooks.md)。
