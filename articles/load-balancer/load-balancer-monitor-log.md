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
ms.date: 05/05/2020
ms.author: allensu
ms.openlocfilehash: 7563eb4d22048021886925f6864e3616bed83a75
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858764"
---
# <a name="azure-monitor-logs-for-public-basic-load-balancer"></a>公共基本负载均衡器的 Azure Monitor 日志

可以在 Azure 中使用不同类型的日志对基本负载均衡器进行管理和故障排除。 可通过门户访问其中某些日志。 可以将日志流式传输到事件中心或 Log Analytics 工作区。 所有日志都可从 Azure blob 存储提取，并在不同的工具（如 Excel 和 Power BI）中查看。  可从下表了解有关各种类型日志的详细信息。

* **活动日志：** 你可以使用["查看活动日志" 监视资源的操作](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit)，以查看提交到 Azure 订阅的所有活动及其状态。 默认情况下，活动日志处于启用状态，可以在 Azure 门户中查看。
* **警报事件日志：** 可以使用此日志查看负载均衡器引发的警报。 每隔五分钟收集一次负载均衡器的状态。 仅在引发了负载均衡器警报事件的情况下，才会向此日志写入相关内容。
* **运行状况探测日志：** 可以使用此日志查看运行状况探测器检测到的问题，例如后端池中由于运行状况探测失败未从负载均衡器接收请求的实例数。 当运行状况探测状态发生更改时，将写入此日志。

> [!IMPORTANT]
> Azure Monitor 日志当前仅适用于公共基本负载均衡器。 日志仅适用于在资源管理器部署模型中部署的资源。 不能将日志用于经典部署模型中的资源。 有关部署模型的详细信息，请参阅[了解 Resource Manager 部署和经典部署](../azure-resource-manager/management/deployment-models.md)。

## <a name="enable-logging"></a>启用日志记录

每个 Resource Manager 资源都会自动启用活动日志记录。 启用事件和运行状况探测日志记录，开始收集通过这些日志提供的数据。 使用以下步骤启用日志记录。

登录 [Azure 门户](https://portal.azure.com)。 如果还没有负载均衡器，请先[创建负载均衡器](https://docs.microsoft.com/azure/load-balancer/quickstart-create-basic-load-balancer-portal)，再继续。

1. 在门户中，单击 "**资源组**"。
2. 选择负载均衡器所在>的 " ** \<资源组名称**"。
3. 选择负载均衡器。
4. 选择 "**监视** > **诊断设置**"。
5. 在 "**诊断设置**" 窗格的 "**诊断设置**" 下，选择 " **+ 添加诊断设置**"。
6. 在 "**诊断设置**" 创建窗格的 "**名称**" 字段中，输入**myLBDiagnostics** 。
7. **诊断设置**有三个选项。  你可以选择一个、两个或全部三个，并根据你的要求进行配置：
   * **存档到存储帐户**
   * **流式传输到事件中心**
   * **发送到 Log Analytics**

    ### <a name="archive-to-a-storage-account"></a>存档到存储帐户
    你需要已为此进程创建的存储帐户。  若要创建存储帐户，请参阅[创建存储帐户](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)

    1. 选中 "**存档到存储帐户**" 旁边的复选框。
    2. 选择 "**配置**" 以打开 "**选择存储帐户**" 窗格。
    3. 在下拉框中，选择在其中创建了你的存储帐户的**订阅**。
    4. 在下拉框中的 "**存储帐户**" 下选择存储帐户的名称。
    5. 选择“确定”。

    ### <a name="stream-to-an-event-hub"></a>流式传输到事件中心
    你将需要已为此进程创建的事件中心。  若要创建事件中心，请参阅[快速入门：使用 Azure 门户创建事件中心](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)

    1. 选中 "**流式传输到事件中心**" 旁的复选框
    2. 选择 "**配置**" 以打开 "**选择事件中心**" 窗格。
    3. 在下拉框中，选择在其中创建了事件中心的**订阅**。
    4. 在下拉框中**选择 "事件中心命名空间**"。
    5. 在下拉框中**选择 "事件中心策略名称**"。
    6. 选择“确定”。

    ### <a name="send-to-log-analytics"></a>发送到 Log Analytics
    你需要已为此进程创建并配置了 log analytics 工作区。  若要创建 Log Analytics 工作区，请参阅[在 Azure 门户中创建 Log Analytics 工作区](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)

    1. 选中 "**发送到 Log Analytics**" 旁边的复选框。
    2. 在下拉框中选择 Log Analytics 工作区所在的**订阅**。
    3. 在下拉框中选择 " **Log Analytics" 工作区**。


8. 在 "**诊断设置**" 窗格中的 "**日志**" 部分下，选中 "两者" 旁边的复选框：
   * **LoadBalancerAlertEvent**
   * **LoadBalancerProbeHealthStatus**

9.  在 "**诊断设置**" 窗格的 "**指标**" 部分下，选中 "以下项" 旁边的复选框：
   * **AllMetrics**

11. 验证所有内容是否正确，并单击 "创建**诊断设置**" 窗格顶部的 "**保存**"。

## <a name="activity-log"></a>活动日志

默认情况下会生成活动日志。 日志在 Azure 的事件日志存储区中保留 90 天。 若要深入了解这些日志，请阅读[查看活动日志以监视资源的操作](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit)一文。

## <a name="archive-to-storage-account-logs"></a>存档到存储帐户日志

### <a name="alert-event-log"></a>警报事件日志

只有基于每个负载均衡器启用了此日志，才会生成此日志。 事件以 JSON 格式记录，并存储在启用日志记录时指定的存储帐户中。 下面的示例是一个事件。

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

JSON 输出显示 "*事件名称*" 属性，该属性将描述负载均衡器创建警报的原因。 在这种情况下，生成的警报是由源 IP NAT 限制（SNAT）引起的 TCP 端口耗尽导致的。

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

JSON 输出在属性字段显示了探测运行状况的基本信息。 *DipDownCount*属性显示后端上的实例总数，这些实例未收到网络流量，原因是探测响应失败。

### <a name="view-and-analyze-the-activity-log"></a>查看和分析活动日志

可以使用任何以下方法查看和分析活动日志数据：

* **Azure 工具：** 通过 Azure PowerShell、Azure 命令行接口（CLI）、Azure REST API 或 Azure 门户从活动日志中检索信息。 [使用 Resource Manager 审核操作](../azure-resource-manager/management/view-activity-logs.md)一文中详细介绍了每种方法的分步说明。
* **Power BI：** 如果还没有 [Power BI](https:// .microsoft.com/pricing) 帐户，可以免费试用。 使用[适用于 Power BI 的 Azure 审核日志内容包](https:// .microsoft.com/documentation/ -content-pack-azure-audit-logs)，可以借助预配置的仪表板分析数据，也可以自定义视图来满足自己的要求。

### <a name="view-and-analyze-the-health-probe-and-event-log"></a>查看和分析运行状况探测和事件日志

连接到存储帐户并检索事件和运行状况探测日志的 JSON 日志条目。 下载 JSON 文件后，可以将它们转换为 CSV 并在 Excel、Power BI 或任何其他数据可视化工具中查看。

> [!TIP]
> 如果熟悉 Visual Studio 和更改 C# 中的常量和变量值的基本概念，则可以使用 GitHub 提供的[日志转换器工具](https://github.com/Azure-Samples/networking-dotnet-log-converter)。

## <a name="stream-to-an-event-hub"></a>流式传输到事件中心
将诊断信息流式传输到事件中心时，可以将其用于在 Azure Monitor 集成的第三方 SIEM 工具中进行集中式日志分析。 有关详细信息，请参阅[将 Azure 监视数据流式传输到事件中心](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration)

## <a name="send-to-log-analytics"></a>发送到 Log Analytics
Azure 中的资源可以将其诊断信息直接发送到 Log Analytics 工作区，在此工作区中，可以针对疑难解答和分析信息运行复杂的查询。  有关详细信息，请参阅[在 Azure Monitor 中 Log Analytics 工作区收集 Azure 资源日志](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace)

## <a name="next-steps"></a>后续步骤

[了解负载均衡器探测](load-balancer-custom-probe-overview.md)
