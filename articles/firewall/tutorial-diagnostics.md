---
title: 教程 - 监视 Azure 防火墙日志
description: 本教程介绍如何启用和管理 Azure 防火墙日志。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 7/11/2018
ms.author: victorh
ms.openlocfilehash: a4922fda80b957138a9929090f9d3c349348185d
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38991839"
---
# <a name="tutorial-monitor-azure-firewall-logs"></a>教程：监视 Azure 防火墙日志

[!INCLUDE [firewall-preview-notice](../../includes/firewall-preview-notice.md)]

Azure 防火墙文章中的示例假设已启用了Azure 防火墙公共预览版。 有关详细信息，请参阅[启用 Azure 防火墙公共预览版](public-preview.md)。

可以使用防火墙日志来监视 Azure 防火墙。 此外，可以使用活动日志来审核对 Azure 防火墙资源执行的操作。

可通过门户访问其中部分日志。 可将日志发送到 [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md)、存储和事件中心，并使用 Log Analytics 或其他工具（例如 Excel 和 Power BI）对其进行分析。

本教程介绍如何执行以下操作：

> [!div class="checklist"]
> * 通过 Azure 门户启用日志记录
> * 使用 PowerShell 启用日志记录
> * 查看和分析活动日志
> * 查看和分析网络与应用程序规则日志

## <a name="diagnostic-logs"></a>诊断日志

 以下诊断日志适用于 Azure 防火墙：

* **应用程序规则日志**

   仅当为每个 Azure 防火墙启用了应用程序规则日志时，才会将此日志保存到存储帐户、流式传输到事件中心，和/或发送到 Log Analytics。 每当建立与某个配置的应用程序规则匹配的新连接，就会为接受/拒绝的连接生成一条日志。 如以下示例中所示，数据以 JSON 格式记录：

   ```
   Category: access logs are either application or network rule logs.
   Time: log timestamp.
   Properties: currently contains the full message. 
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
   {
    "category": "AzureFirewallApplicationRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallApplicationRuleLog",
    "properties": {
        "msg": "HTTPS request from 10.1.0.5:55640 to mydestination.com:443. Action: Allow. Rule Collection: collection1000. Rule: rule1002"
    }
   }
   ```

* **网络规则日志**

   仅当为每个 Azure 防火墙启用了网络规则日志时，才会将此日志保存到存储帐户、流式传输到事件中心，和/或发送到 Log Analytics。 每当建立与某个配置的网络规则匹配的新连接，就会为接受/拒绝的连接生成一条日志。 如以下示例中所示，数据以 JSON 格式记录：

   ```
   Category: access logs are either application or network rule logs.
   Time: log timestamp.
   Properties: currently contains the full message. 
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
  {
    "category": "AzureFirewallNetworkRule",
    "time": "2018-06-14T23:44:11.0590400Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallNetworkRuleLog",
    "properties": {
        "msg": "TCP request from 111.35.136.173:12518 to 13.78.143.217:2323. Action: Deny"
    }
   }

   ```

可通过三种方式存储日志：

* 存储帐户：如果日志存储时间较长并且希望能根据需要随时查看，则最好使用存储帐户。
* 事件中心：若要集成其他安全信息和事件管理 (SEIM) 工具，获取资源警报，则事件中心是很好的选择。
* Log analytics：Log analytics 最适合用于应用程序常规实时监视或查看趋势。

## <a name="activity-logs"></a>活动日志

   默认情况下会收集活动日志条目，可在 Azure 门户中查看这些条目。

   可以使用 [Azure 活动日志](../azure-resource-manager/resource-group-audit.md)（以前称为运行日志和审核日志）查看提交到 Azure 订阅的所有操作。

## <a name="enable-diagnostic-logging-through-the-azure-portal"></a>通过 Azure 门户启用诊断日志记录

完成此过程启用诊断日志记录后，可能需要经过几分钟，数据才会显示在日志中。 如果一开始未看到任何内容，请在几分钟后重新查看。

1. 在 Azure 门户中，打开防火墙资源组并单击防火墙。
2. 在“监视”下面，单击“诊断日志”。

   Azure 防火墙有两个特定于服务的日志：

   * 应用程序规则日志
   * 网络规则日志

3. 若要开始收集数据，请单击“启用诊断”。
4. “诊断设置”页提供用于诊断日志的设置。 
5. 在此示例中，Log Analytics 存储日志，因此请键入“防火墙日志分析”作为名称。
6. 单击“发送到 Log Analytics”以配置工作区。 也可使用事件中心和存储帐户保存诊断日志。
7. 在“Log Analytics”下面，单击“配置”。
8. 在“OMS 工作区”页中，单击“创建新工作区”。
9. 在“Log Analytics 工作区”页中，键入 **firewall-oms** 作为新 **OMS 工作区**的名称。
10. 选择订阅，使用现有的防火墙资源组 (**Test-FW-RG**)，选择“美国东部”作为位置，然后选择“免费”定价层。
11. 单击“确定”。
   ![开始配置过程][1]
12. 在“日志”下面，单击“AzureFirewallApplicationRule”和“AzureFirewallNetworkRule”收集应用程序和网络规则的日志。
   ![保存诊断设置][2]
13. 单击“ **保存**”。

## <a name="enable-logging-with-powershell"></a>使用 PowerShell 启用日志记录

每个 Resource Manager 资源都会自动启用活动日志记录。 必须启用诊断日志记录才能开始收集通过这些日志提供的数据。

若要启用诊断日志记录，请使用以下步骤：

1. 记下存储日志数据的存储帐户资源 ID。 此值采用以下格式：*/subscriptions/\<订阅 ID\>/resourceGroups/\<资源组名称\>/providers/Microsoft.Storage/storageAccounts/\<存储帐户名称\>*。

   订阅中的所有存储帐户均可使用。 可使用 Azure 门户查找此信息。 此信息位于资源的“属性”页中。

2. 记下为其启用了日志记录的防火墙的资源 ID。 此值采用以下格式：*/subscriptions/\<订阅 ID\>/resourceGroups/\<资源组名称\>/providers/Microsoft.Network/azureFirewalls/\<防火墙名称\>*。

   可使用门户查找此信息。

3. 使用以下 PowerShell cmdlet 启用诊断日志记录：

    ```powershell
    Set-AzureRmDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name> `
   -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> `
   -Enabled $true     
    ```
    
> [!TIP] 
>诊断日志不需要单独的存储帐户。 使用存储来记录访问和性能需支付服务费用。

## <a name="view-and-analyze-the-activity-log"></a>查看和分析活动日志

可使用以下任一方法查看和分析活动日志数据：

* Azure 工具：通过 Azure PowerShell、Azure CLI、Azure REST API 或 Azure 门户检索活动日志中的信息。 [使用 Resource Manager 活动操作](../azure-resource-manager/resource-group-audit.md)一文中详细介绍了每种方法的分步说明。
* Power BI：如果尚无 [Power BI](https://powerbi.microsoft.com/pricing) 帐户，可免费试用。 使用[适用于 Power BI 的 Azure 活动日志内容包](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/)，可以借助预配置的仪表板（可直接使用或进行自定义）分析数据。

## <a name="view-and-analyze-the-network-and-application-rule-logs"></a>查看和分析网络与应用程序规则日志

Azure [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md) 收集计数器和事件日志文件。 它含有可视化和强大的搜索功能，可用于分析日志。

还可以连接到存储帐户并检索访问和性能日志的 JSON 日志条目。 下载 JSON 文件后，可以将它们转换为 CSV 并在 Excel、Power BI 或任何其他数据可视化工具中查看。

> [!TIP]
> 如果熟悉 Visual Studio 和更改 C# 中的常量和变量值的基本概念，则可以使用 GitHub 提供的[日志转换器工具](https://github.com/Azure-Samples/networking-dotnet-log-converter)。


## <a name="next-steps"></a>后续步骤

将防火墙配置为收集日志后，可以浏览 Log Anaytics 以查看数据。

> [!div class="nextstepaction"]
> [Log Analytics 中的网络监视解决方案](../log-analytics/log-analytics-azure-networking-analytics.md)

[1]: ./media/tutorial-diagnostics/figure1.png
[2]: ./media/tutorial-diagnostics/figure2.png
