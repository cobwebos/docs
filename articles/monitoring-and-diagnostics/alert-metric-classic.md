---
title: 使用 Azure Monitor 创建、查看和管理经典指标警报
description: 了解如何使用 Azure 门户、CLI 或 Powershell 来创建、查看和管理经典指标警报规则。
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: snmuvva
ms.component: alerts
ms.openlocfilehash: e325335d43ef31c44ac812aca66309132f5372a3
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46951604"
---
# <a name="create-view-and-manage-metric-alerts-using-azure-monitor"></a>使用 Azure Monitor 创建、查看和管理指标警报

Azure Monitor 中的经典指标警报提供了一种在指标超出阈值时获得通知的方法。 经典指标警报是一个较旧的功能，它仅可针对非维度指标发出警报。 有一个称为“指标警报”的较新功能，它具有比经典指标警报改进的功能。 可以在[指标警报概述](alert-metric-overview.md)中了解新的指标警报功能的详细信息。 在本文中，我们将介绍如何通过 Azure 门户、Azure CLI 和 Powershell 创建、查看和管理经典指标警报规则。

## <a name="create-a-classic-metric-alert-rule-using-azure-portal"></a>使用 Azure 门户创建经典指标警报规则

1. 在[门户](https://portal.azure.com/)中，找到要监视的资源，然后选择它。

2. 在“监视”部分中，选择“警报(经典)”。 对于不同的资源，文本和图标可能会略有不同。 如果在此处未找到“警报(经典)”，则它可能位于“警报”或“警报规则”中。

    ![监视](./media/alerts-metric-classic/AlertRulesButton.png)

3. 选择“添加指标警报(经典)”命令，并填写字段。

    ![添加警报](./media/alerts-metric-classic/AddAlertOnlyParamsPage.png)

4. **命名**警报规则。 然后选择也在通知电子邮件中显示的“说明”。

5. 选择要监视的指标。 然后为该指标选择“条件”和“阈值”值。 还选择触发警报前指标规则必须满足的时间段。 例如，如果使用时间段“过去 5 分钟”，且警报针对 CPU 高于 80% 的情况，则 CPU 持续高于 80% 达到 5 分钟时将触发警报。 第一次触发后，CPU 5 分钟内持续低于 80% 时会再次触发警报。 CPU 指标度量每分钟进行一次。

6. 如果希望在触发警报时管理员和共同管理员收到电子邮件通知，则选择“向所有者发送电子邮件...”。

7. 如果希望在触发警报时向其他电子邮件发送通知，请将其添加到“其他管理员电子邮件”字段。 使用分号分隔多个电子邮件，格式如下：*email@contoso.com; email2@contoso.com*

8. 如果希望在触发警报时调用有效的 URI，请将其放入“Webhook”字段中。

9. 如果使用 Azure 自动化时，则可选择在触发警报时要运行的 Runbook。

10. 选择“确定”以创建警报。

几分钟后，警报将处于活动状态，并按前面所述进行触发。

## <a name="manage-your-classic-metric-alert-rules-using-azure-portal"></a>使用 Azure 门户管理经典指标警报规则

创建警报后，可以选择它并执行以下任务之一：

* 查看显示指标阈值和前一天实际值的关系图。
* 编辑或删除其。
* 如果想要暂时停止或恢复接收该警报的通知，可**禁用**或**启用**它。

## <a name="creating-and-managing-classic-metric-alert-rule-using-azure-cli"></a>使用 Azure CLI 创建和管理经典指标警报规则

前面几部分介绍了如何使用 Azure 门户创建、查看和管理指标警报规则。 本部分将介绍如何使用跨平台 [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/get-started-with-azure-cli?view=azure-cli-latest) 执行同样的事情。 开始使用 Azure CLI 的最快方式是通过 [Azure Cloud Shell](https://docs.microsoft.com/en-us/azure/cloud-shell/overview?view=azure-cli-latest)。

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

### <a name="create-view-and-manage-classic-metric-alerts-using-powershell"></a>使用 PowerShell 创建、查看和管理经典指标警报

本部分展示了如何使用 PowerShell 命令创建、查看和管理经典指标警报。本文中的示例展示了如何将 Azure Monitor cmdlet 用于经典指标警报。

1. 如果尚未安装，请在计算机上安装要运行的 PowerShell。 有关详细信息，请参阅[如何安装和配置 PowerShell](/powershell/azure/overview)。 还可以在 [Azure 监视器 (Insights) Cmdlet](https://docs.microsoft.com/powershell/module/azurerm.insights) 上查看 Azure 监视器 PowerShell cmdlet 的完整列表。

2. 首先，登录到 Azure 订阅。

    ```PowerShell
    Connect-AzureRmAccount
    ```

3. 会出现登录界面。 登录帐户后，会出现 TenantID 和默认订阅 ID。 所有 Azure cmdlets 都在默认订阅的上下文中工作。 若要查看有权访问的订阅的列表，请使用以下命令：

    ```PowerShell
    Get-AzureRmSubscription
    ```

4. 若要将工作环境更改为另一订阅，请使用以下命令：

    ```PowerShell
    Set-AzureRmContext -SubscriptionId <subscriptionid>
    ```

5. 可以检索某个资源组中的所有经典指标警报规则：

    ```PowerShell
    Get-AzureRmAlertRule -ResourceGroup montest
    ```

6. 可以查看经典指标警报规则的详细信息

    ```PowerShell
    Get-AzureRmAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
    ```

7. 可以检索为目标资源设置的所有警报规则。 例如，虚拟机上的所有警报规则设置。

    ```PowerShell
    Get-AzureRmAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig

8. You can use the `Add-AlertRule` cmdlet to create, update, or disable an alert rule. You can create email and webhook properties using  `New-AzureRmAlertRuleEmail` and `New-AzureRmAlertRuleWebhook`, respectively. In the Alert rule cmdlet, assign these properties as actions to the **Actions** property of the Alert Rule. The following table describes the parameters and values used to create an alert using a metric.

    | parameter | value |
    | --- | --- |
    | Name |simpletestdiskwrite |
    | Location of this alert rule |East US |
    | ResourceGroup |montest |
    | TargetResourceId |/subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig |
    | MetricName of the alert that is created |\PhysicalDisk(_Total)\Disk Writes/sec. See the `Get-MetricDefinitions` cmdlet about how to retrieve the exact metric names |
    | operator |GreaterThan |
    | Threshold value (count/sec in for this metric) |1 |
    | WindowSize (hh:mm:ss format) |00:05:00 |
    | aggregator (statistic of the metric, which uses Average count, in this case) |Average |
    | custom emails (string array) |'foo@example.com','bar@example.com' |
    | send email to owners, contributors and readers |-SendToServiceOwners |

9. Create an Email action

    ```PowerShell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
    ```

10. 创建 Webhook 操作

    ```PowerShell
    $actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
    ```

## <a name="next-steps"></a>后续步骤

- [使用资源管理器模板创建经典指标警报](monitoring-enable-alerts-using-template.md)。
- [使用 Webhook 让经典指标警报通知非 Azure 系统](insights-webhooks-alerts.md)。

