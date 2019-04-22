---
title: 收集到 Log Analytics 工作区的 Azure 服务日志和指标 |Microsoft Docs
description: 若要将日志和度量值写入到 Azure Monitor 中的 Log Analytics 工作区的 Azure 资源上配置诊断。
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 84105740-3697-4109-bc59-2452c1131bfe
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/12/2017
ms.author: magoedte
ms.openlocfilehash: d086b6f844deb06d98edec8d8ec0f5670d84f066
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59006267"
---
# <a name="collect-azure-service-logs-and-metrics-into-log-analytics-workspace-in-azure-monitor"></a>收集到 Log Analytics 工作区中 Azure Monitor 的 Azure 服务日志和指标

有四种不同方式可收集 Azure 服务的日志和指标：

1. Azure 诊断定向到 Azure Monitor 中的 Log Analytics 工作区 (*诊断*下表中)
2. Azure 诊断定向到 Azure 存储到 Azure Monitor 中的 Log Analytics 工作区 (*存储*下表中)
3. Azure 服务的连接器（下表中的*连接器*）
4. 脚本收集，然后将数据发布到 Azure Monitor （下表中和未列出的服务，则为空白） 中的 Log Analytics 工作区


| 服务                 | 资源类型                           | 日志        | 度量值     | 解决方案 |
| --- | --- | --- | --- | --- |
| 应用程序网关数    | Microsoft.Network/applicationGateways   | 诊断 | 诊断 | [Azure 应用程序网关分析](../insights/azure-networking-analytics.md#azure-application-gateway-analytics-solution-in-azure-monitor) |
| Application insights    |                                         | 连接器   | 连接器   | [Application Insights Connector](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)（预览版） |
| 自动化帐户     | Microsoft.Automation/AutomationAccounts | 诊断 |             | [详细信息](../../automation/automation-manage-send-joblogs-log-analytics.md)|
| 批处理帐户          | Microsoft.Batch/batchAccounts           | 诊断 | 诊断 | |
| 经典云服务  |                                         | 存储     |             | [详细信息](azure-storage-iis-table.md) |
| 认知服务      | Microsoft.CognitiveServices/accounts    |             | 诊断 | |
| Data Lake Analytics     | Microsoft.DataLakeAnalytics/accounts    | 诊断 |             | |
| Data Lake Store         | Microsoft.DataLakeStore/accounts        | 诊断 |             | |
| 事件中心命名空间     | Microsoft.EventHub/namespaces           | 诊断 | 诊断 | |
| IoT 中心                | Microsoft.Devices/IotHubs               |             | 诊断 | |
| Key Vault               | Microsoft.KeyVault/vaults               | 诊断 |             | [密钥保管库分析](../insights/azure-key-vault.md) |
| 负载均衡器          | Microsoft.Network/loadBalancers         | 诊断 |             |  |
| 逻辑应用              | Microsoft.Logic/workflows <br> Microsoft.Logic/integrationAccounts | 诊断 | 诊断 | |
| 网络安全组 | Microsoft.Network/networksecuritygroups | 诊断 |             | [Azure 网络安全组分析](../insights/azure-networking-analytics.md#azure-network-security-group-analytics-solution-in-azure-monitor) |
| 恢复保管库         | Microsoft.RecoveryServices/vaults       |             |             | [Azure 恢复服务分析（预览版）](https://github.com/krnese/AzureDeploy/blob/master/OMS/MSOMS/Solutions/recoveryservices/)|
| 搜索服务         | Microsoft.Search/searchServices         | 诊断 | 诊断 | |
| 服务总线命名空间   | Microsoft.ServiceBus/namespaces         | 诊断 | 诊断 | [Service Fabric 分析（预览版）](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-servicebus-solution)|
| Service Fabric          |                                         | 存储     |             | [Service Fabric 分析（预览版）](../../service-fabric/service-fabric-diagnostics-oms-setup.md) |
| SQL (v12)               | Microsoft.Sql/servers/databases <br> Microsoft.Sql/servers/elasticPools |             | 诊断 | [Azure SQL Analytics（预览版）](../insights/azure-sql.md) |
| 存储                 |                                         |             | 脚本      | [Azure 存储分析（预览版）](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-azure-storage-analytics-solution) |
| 虚拟机        | Microsoft.Compute/virtualMachines       | 分机   | 分机 <br> 诊断  | |
| 虚拟机规模集 | Microsoft.Compute/virtualMachines <br> Microsoft.Compute/virtualMachineScaleSets/virtualMachines |             | 诊断 | |
| Web 服务器场        | Microsoft.Web/serverfarms               |             | 诊断 | |
| 网站               | Microsoft.Web/sites <br> Microsoft.Web/sites/slots |             | 诊断 | [Azure Web 应用分析（预览版）](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-web-apps-analytics) |


> [!NOTE]
> 若要监视 Azure 虚拟机（Linux 和 Windows），建议安装 [Log Analytics VM 扩展](../../azure-monitor/learn/quick-collect-azurevm.md)。 该代理提供从虚拟机中收集到的见解。 对于虚拟机规模集，还可以使用扩展。
>
>

## <a name="azure-diagnostics-direct-to-log-analytics"></a>将 Azure 诊断定向到 Log Analytics
许多 Azure 资源都可以将诊断日志和指标直接写入到 Azure Monitor 中的 Log Analytics 工作区，这是收集数据以进行分析的首选的方法。 数据时使用 Azure 诊断，将立即写入到工作区中，并且没有无需首先将数据写入到存储。

支持的 azure 资源[Azure 监视器](../../azure-monitor/overview.md)可以直接向 Log Analytics 工作区中发送的日志和指标。

> [!NOTE]
> 当前不支持将多维指标发送到 Log Analytics 工作区通过诊断设置。 多维指标将按平展后的单维指标导出，并跨维值聚合。
>
> 例如：可以基于每个队列级别浏览和绘制事件中心上的“传入消息”指标。 但是，当通过诊断设置导出时，该指标将表示为事件中心的所有队列中的所有传入消息。
>
>

* 有关可用指标的详细信息，请参阅 [Azure 监视器支持的指标](../../azure-monitor/platform/metrics-supported.md)。
* 有关可用日志的详细信息，请参阅[诊断日志支持的服务和架构](../../azure-monitor/platform/diagnostic-logs-schema.md)。

### <a name="enable-diagnostics-with-powershell"></a>使用 PowerShell 启用诊断

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

下面的 PowerShell 示例演示如何使用[集 AzDiagnosticSetting](/powershell/module/Az.Monitor/Set-AzDiagnosticSetting)启用网络安全组上的诊断。 同一方法适用于所有受支持的资源 - 将 `$resourceId` 设置为要为其启用诊断的资源的资源 ID。

```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$resourceId = "/SUBSCRIPTIONS/ec11ca60-1234-491e-5678-0ea07feae25c/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO"

Set-AzDiagnosticSetting -ResourceId $ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="enable-diagnostics-with-resource-manager-templates"></a>使用 Resource Manager 模板启用诊断

要在资源创建时为其启用诊断，并将诊断发送到 Log Analytics 工作区，可以使用如下模板。 此示例适用于自动化帐户，但适用于所有受支持的资源类型。

```json
        {
            "type": "Microsoft.Automation/automationAccounts/providers/diagnosticSettings",
            "name": "[concat(parameters('omsAutomationAccountName'), '/', 'Microsoft.Insights/service')]",
            "apiVersion": "2015-07-01",
            "dependsOn": [
                "[concat('Microsoft.Automation/automationAccounts/', parameters('omsAutomationAccountName'))]",
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspaceName'))]"
            ],
            "properties": {
                "workspaceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('omsWorkspaceName'))]",
                "logs": [
                    {
                        "category": "JobLogs",
                        "enabled": true
                    },
                    {
                        "category": "JobStreams",
                        "enabled": true
                    }
                ]
            }
        }
```

[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="azure-diagnostics-to-storage-then-to-log-analytics"></a>将 Azure 诊断定向到存储，然后再定向到 Log Analytics

收集从某些资源中的日志，就可以将日志发送到 Azure 存储空间，然后配置要从存储中读取日志的 Log Analytics 工作区。

Azure 监视器可以使用此方法以从以下资源和日志的 Azure 存储收集的诊断：

| 资源 | 日志 |
| --- | --- |
| Service Fabric |ETWEvent <br> 操作事件 <br> 可靠角色事件 <br> 可靠服务事件 |
| 虚拟机 |Linux Syslog <br> Windows 事件 <br> IIS 日志 <br> Windows ETWEvent |
| Web 角色 <br> 辅助角色 |Linux Syslog <br> Windows 事件 <br> IIS 日志 <br> Windows ETWEvent |

> [!NOTE]
> 将诊断发送到存储帐户时，Log Analytics 工作区读取的数据从存储帐户时，会产生费用正常 Azure 数据费率对存储和事务。
>
>

请参阅[blob 存储适用于事件的 IIS 和表存储用于](azure-storage-iis-table.md)若要了解有关 Azure Monitor 可以如何收集这些日志的详细信息。

## <a name="connectors-for-azure-services"></a>Azure 服务的连接器

没有为应用程序的见解，以允许发送到 Log Analytics 工作区的 Application Insights 收集的数据连接器。

详细了解 [Application Insights 连接器](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)。

## <a name="scripts-to-collect-and-post-data-to-log-analytics-workspace"></a>脚本收集，将数据发布到 Log Analytics 工作区

不提供直接的方法来将日志和指标发送到 Log Analytics 工作区的 Azure 服务可以使用 Azure 自动化脚本来收集日志和指标。 该脚本然后可以将数据发送到工作区使用[数据收集器 API](../../azure-monitor/platform/data-collector-api.md)

Azure 模板库有[使用 Azure 自动化的示例](https://azure.microsoft.com/resources/templates/?term=OMS)从服务收集数据并将其发送到 Azure Monitor。

## <a name="next-steps"></a>后续步骤

* [使用适用于 IIS 的 blob 存储和适用于事件的表存储](azure-storage-iis-table.md)，读取将诊断写入到表存储或将 ISS 日志写入到 blob 存储的 Azure 服务的日志。
* [启用解决方案](../../azure-monitor/insights/solutions.md)深入分析数据。
* [使用搜索查询](../../azure-monitor/log-query/log-query-overview.md)分析数据。
