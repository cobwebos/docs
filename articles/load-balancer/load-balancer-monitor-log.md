---
title: 监视公共基本负载均衡器的操作、事件和计数器
titleSuffix: Azure Load Balancer
description: 了解如何为公共基本负载均衡器启用警报事件以及探测运行状况日志记录
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/27/2018
ms.author: allensu
ms.openlocfilehash: 23a3a2629c6f2f89c4b8f6d5af57bcf3b6bb67dd
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74214915"
---
# <a name="azure-monitor-logs-for-public-basic-load-balancer"></a>Azure Monitor logs for public Basic Load Balancer

>[!IMPORTANT] 
>Azure 负载均衡器支持两种不同的类型：“基本”和“标准”。 本文介绍基本负载均衡器。 有关标准负载均衡器的详细信息，请参阅[标准负载均衡器概述](load-balancer-standard-overview.md)，该概述通过 Azure Monitor 中的多维指标公开遥测数据。

可以在 Azure 中使用不同类型的日志对基本负载均衡器进行管理和故障排除。 可通过门户访问其中某些日志。 Logs can be streamed to an event hub or a Log Analytics workspace. All logs can be extracted from Azure blob storage, and viewed in different tools, such as Excel and Power BI.  可从下表了解有关各种类型日志的详细信息。

* **Activity logs:** You can use [View activity logs to monitor actions on resources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit) to view all activity being submitted to your Azure subscription(s), and their status. Activity logs are enabled by default, and can be viewed in the Azure portal.
* **警报事件日志：** 可以使用此日志查看负载均衡器引发的警报。 每隔五分钟收集一次负载均衡器的状态。 仅在引发了负载均衡器警报事件的情况下，才会向此日志写入相关内容。
* **运行状况探测日志：** 可以使用此日志查看运行状况探测器检测到的问题，例如后端池中由于运行状况探测失败未从负载均衡器接收请求的实例数。 当运行状况探测状态发生更改时，将写入此日志。

> [!IMPORTANT]
> Azure Monitor logs currently works only for public Basic load balancers. 日志仅适用于在资源管理器部署模型中部署的资源。 不能将日志用于经典部署模型中的资源。 有关部署模型的详细信息，请参阅[了解 Resource Manager 部署和经典部署](../azure-resource-manager/resource-manager-deployment-model.md)。

## <a name="enable-logging"></a>启用日志记录

每个 Resource Manager 资源都会自动启用活动日志记录。 Enable event and health probe logging to start collecting the data available through those logs. 使用以下步骤启用日志记录。

登录到 [Azure 门户](https://portal.azure.com)。 如果还没有负载均衡器，请先[创建负载均衡器](https://docs.microsoft.com/azure/load-balancer/quickstart-create-basic-load-balancer-portal)，再继续。

1. In the portal, click **Resource groups**.
2. Select **\<resource-group-name>** where your load balancer is.
3. Select your load balancer.
4. Select **Monitoring** > **Diagnostic settings**.
5. In the **Diagnostics settings** pane, under **Diagnostics settings**, select **+ Add diagnostic setting**.
6. In the **Diagnostics settings** creation pane, enter **myLBDiagnostics** in the **Name** field.
7. You have three options for the **Diagnostics settings**.  You can choose one, two or all three and configure each for your requirements:
   * **Archive to a storage account**
   * **Stream to an event hub**
   * **Send to Log Analytics**

    ### <a name="archive-to-a-storage-account"></a>存档到存储帐户
    You'll need a storage account already created for this process.  To create a storage account, see [Create a storage account](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)

    1. Select the checkbox next to **Archive to a storage account**.
    2. Select **Configure** to open the **Select a storage account** pane.
    3. Select the **Subscription** where your storage account was created in the pull-down box.
    4. Select the name of your storage account under **Storage account** in the pull-down box. 
    5. Select OK.

    ### <a name="stream-to-an-event-hub"></a>流式传输到事件中心
    You'll need an event hub already created for this process.  To create an event hub, see [Quickstart: Create an event hub using Azure portal](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)

    1. Select the checkbox next to **Stream to an event hub**
    2. Select **Configure** to open the **Select event hub** pane.
    3. Select the **Subscription** where your event hub was created in the pull-down box.
    4. **Select event hub namespace** in the pull-down box.
    5. **Select event hub policy name** in the pull-down box.
    6. Select OK.

    ### <a name="send-to-log-analytics"></a>发送到 Log Analytics
    You'll need to already have a log analytics workspace created and configured for this process.  To create a Log Analytics workspace, see [Create a Log Analytics workspace in the Azure portal](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)

    1. Select the checkbox next to **Send to Log Analytics**.
    2. Select the **Subscription** where your Log Analytics workspace is in the pull-down box.
    3. Select the **Log Analytics Workspace** in the pull-down box.


8. Beneath the **LOG** section in the **Diagnostics settings** pane, select the check box next to both:
   * **LoadBalancerAlertEvent**
   * **LoadBalancerProbeHealthStatus**

9.  Beneath the **METRIC** section in the **Diagnostics settings** pane, select the check box next to:
   * **AllMetrics**

11. Verify everything looks correct and click **Save** at the top of the create **Diagnostic settings** pane.

## <a name="activity-log"></a>活动日志

The activity log is generated by default. 日志在 Azure 的事件日志存储区中保留 90 天。 Learn more about these logs by reading the [View activity logs to monitor actions on resources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit) article.

## <a name="archive-to-storage-account-logs"></a>Archive to storage account logs

### <a name="alert-event-log"></a>警报事件日志

只有基于每个负载均衡器启用了此日志，才会生成此日志。 事件以 JSON 格式记录，并存储在启用日志记录时指定的存储帐户中。 The following example is of an event.

```json
{
    "time": "2016-01-26T10:37:46.6024215Z",
    "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
    "category": "LoadBalancerAlertEvent",
    "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
    "operationName": "LoadBalancerProbeHealthStatus",
    "properties": {
        "eventName": "Resource Limits Hit",
        "eventDescription": "Ports exhausted",
        "eventProperties": {
            "public ip address": "40.117.227.32"
        }
    }
}
```

The JSON output shows the *eventname* property, which will describe the reason for the load balancer created an alert. In this case, the alert generated was because of TCP port exhaustion caused by source IP NAT limits (SNAT).

### <a name="health-probe-log"></a>运行状况探测日志

仅当按如上所述基于每个负载均衡器启用此日志后，才会生成此日志。 数据存储在启用日志记录时指定的存储帐户中。 创建了名为“insights-logs-loadbalancerprobehealthstatus”的容器并记录了以下数据：

```json
{
    "records":[
    {
        "time": "2016-01-26T10:37:46.6024215Z",
        "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
        "category": "LoadBalancerProbeHealthStatus",
        "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
        "operationName": "LoadBalancerProbeHealthStatus",
        "properties": {
            "publicIpAddress": "40.83.190.158",
            "port": "81",
            "totalDipCount": 2,
            "dipDownCount": 1,
            "healthPercentage": 50.000000
        }
    },
    {
        "time": "2016-01-26T10:37:46.6024215Z",
        "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
        "category": "LoadBalancerProbeHealthStatus",
        "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
        "operationName": "LoadBalancerProbeHealthStatus",
        "properties": {
            "publicIpAddress": "40.83.190.158",
            "port": "81",
            "totalDipCount": 2,
            "dipDownCount": 0,
            "healthPercentage": 100.000000
        }
    }]
}
```

JSON 输出在属性字段显示了探测运行状况的基本信息。 The *dipDownCount* property shows the total number of instances on the back-end, which are not receiving network traffic because of failed probe responses.

### <a name="view-and-analyze-the-audit-log"></a>查看和分析审核日志

可以使用任何以下方法查看和分析审核日志数据：

* **Azure tools:** Retrieve information from the audit logs through Azure PowerShell, the Azure Command Line Interface (CLI), the Azure REST API, or the Azure portal. [使用 Resource Manager 审核操作](../azure-resource-manager/resource-group-audit.md)一文中详细介绍了每种方法的分步说明。
* **Power BI：** 如果还没有 [Power BI](https:// .microsoft.com/pricing) 帐户，可以免费试用。 使用[适用于 Power BI 的 Azure 审核日志内容包](https:// .microsoft.com/documentation/ -content-pack-azure-audit-logs)，可以借助预配置的仪表板分析数据，也可以自定义视图来满足自己的要求。

### <a name="view-and-analyze-the-health-probe-and-event-log"></a>查看和分析运行状况探测和事件日志

Connect to your storage account and retrieve the JSON log entries for event and health probe logs. Once you download the JSON files, you can convert them to CSV and view in Excel, Power BI, or any other data visualization tool.

> [!TIP]
> 如果熟悉 Visual Studio 和更改 C# 中的常量和变量值的基本概念，则可以使用 GitHub 提供的[日志转换器工具](https://github.com/Azure-Samples/networking-dotnet-log-converter)。

## <a name="stream-to-an-event-hub"></a>流式传输到事件中心
When diagnostic information is streamed to an event hub, it can be used for centralized log analysis in a third-party SIEM tool with Azure Monitor Integration. For more information, see [Stream Azure monitoring data to an event hub](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs#tools-with-azure-monitor-integration)

## <a name="send-to-log-analytics"></a>发送到 Log Analytics
Resources in Azure can have their diagnostic information sent directly to a Log Analytics workspace where complex queries can be run against the information for troubleshooting and analysis.  For more information, see [Collect Azure resource logs in Log Analytics workspace in Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace)

## <a name="next-steps"></a>后续步骤

[了解负载均衡器探测](load-balancer-custom-probe-overview.md)
