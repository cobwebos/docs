---
title: Azure Service Fabric - 使用 Log Analytics 设置性能监视 | Microsoft Docs
description: 了解如何设置 Log Analytics 代理以监视 Azure Service Fabric 群集的容器和性能计数器。
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/16/2018
ms.author: srrengar
ms.openlocfilehash: b97a32e2e859a5bb370873bfbdc5c6b4dffa1ac1
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2018
ms.locfileid: "39237836"
---
# <a name="performance-monitoring-with-log-analytics"></a>使用 Log Analytics 设置性能监视

本文介绍如何逐步将 Log Analytics 代理作为虚拟机规模集扩展添加到群集并将其连接到现有的 Azure Log Analytics 工作区。 这可收集关于容器、应用程序和性能监视的诊断数据。 通过将其作为扩展添加到虚拟机规模集资源，Azure 资源管理器可确保它安装在每个节点上，即使在缩放群集时也是如此。

> [!NOTE]
> 本文假定已设置了 Azure Log Analytics 工作区。 如果尚未设置，请转到[设置 Azure Log Analytics](service-fabric-diagnostics-oms-setup.md)

## <a name="add-the-agent-extension-via-azure-cli"></a>通过 Azure CLI 添加代理扩展

将 Log Analytics 代理添加到群集的最佳方法是使用 Azure CLI 提供的虚拟机规模集 API。 如果尚未设置 Azure CLI，请转至 Azure 门户并打开 [Cloud Shell](../cloud-shell/overview.md) 实例，或[安装 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)。

1. 请求 Cloud Shell 之后，请确保在资源所在的订阅中工作。 请使用 `az account show` 进行检查，并确保“名称”值与群集订阅的值匹配。

2. 在门户中，导航到 Log Analytics 工作区所在的资源组。 单击进入 Log Analytics 资源（资源类型为 Log Analytics）。 进入资源概述页后，单击左侧菜单中“设置”部分下面的“高级设置”。

    ![Log Analytics 属性页](media/service-fabric-diagnostics-oms-agent/oms-advanced-settings.png)
 
3. 若要建立 Windows 群集，请单击“Windows 服务器”；若要创建 Linux 群集，请单击“Linux 服务器”。 此页将显示 `workspace ID` 和 `workspace key`（在门户中列为“主键”）。 下一步骤需要使用这两个值。

4. 使用 Cloud Shell 中的 `vmss extension set` API 运行命令，将 Log Analytics 代理安装到群集中：

    对于 Windows 群集：
    
    ```sh
    az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<Log AnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<Log AnalyticsworkspaceKey>'}"
    ```

    对于 Linux 群集：

    ```sh
    az vmss extension set --name OmsAgentForLinux --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<Log AnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<Log AnalyticsworkspaceKey>'}"
    ```

    以下示例展示如何将 Log Analytics 代理添加到 Windows 群集。

    ![Log Analytics 代理 cli 命令](media/service-fabric-diagnostics-oms-agent/cli-command.png)
 
5. 15 分钟内即可将代理成功添加到节点上。 可使用 `az vmss extension list` API 验证是否已添加代理：

    ```sh
    az vmss extension list --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType>
    ```

## <a name="add-the-agent-via-the-resource-manager-template"></a>通过资源管理器模板添加代理

部署 Azure Log Analytics 工作区并将代理添加到每个节点的示例资源管理器模板可用于 [Windows](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Windows) 或 [Linux](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux)。

可下载和修改此模板以部署最适合需求的群集。

## <a name="view-performance-counters"></a>查看性能计数器

添加 Log Analytics 代理后，请转到 Log Analytics 门户，选择要收集的性能计数器。 

1. 在 Azure 门户中，转到在其中创建 Service Fabric 分析解决方案的资源组。 选择 **ServiceFabric\<nameOfLog AnalyticsWorkspace\>**。

2. 单击“Log Analytics”。

3. 单击“高级设置”。

4. 单击“数据”，然后单击“Windows 或 Linux 性能计数器”。 此时会显示一个可以选择的默认计数器列表，此外还可以设置收集间隔。 还可以添加要收集的[其他性能计数器](service-fabric-diagnostics-event-generation-perf.md)。 此[参考文章](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85).aspx)中介绍了正确的格式。

5. 单击“保存”，然后单击“确定”。

6. 关闭“高级设置”边栏选项卡。

7. 在“常规”标题下，单击“概述”。

8. 将看到每个已启用的解决方案的图形形式的磁贴，包括 Service Fabric 的磁贴。 单击 **Service Fabric** 图形以转到 Service Fabric 分析解决方案。

9. 将会看到一些带有有关操作通道和 Reliable Services 事件的图形的磁贴。 已选择的计数器的流入数据的图形表示形式将会显示在“节点指标”下。 

10. 单击“容器指标”图形可了解更多详细信息。 还可以使用 Kusto 查询语言，像查询群集事件一样查询性能计数器数据，以及基于节点、性能计数器名称和值进行筛选。

![Log Analytics 性能计数器查询](media/service-fabric-diagnostics-event-analysis-oms/oms_node_metrics_table.PNG)

## <a name="next-steps"></a>后续步骤

* 收集相关[性能计数器](service-fabric-diagnostics-event-generation-perf.md)。 若要配置 Log Analytics 代理以收集特定性能计数器，请查看[配置数据源](../log-analytics/log-analytics-data-sources.md#configuring-data-sources)。
* 配置 Log Analytics，设置[自动警报](../log-analytics/log-analytics-alerts.md)，以辅助检测和诊断
* 作为替代方法，可以通过 [Azure 诊断扩展收集性能计数器并将其发送到 Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md#add-the-ai-sink-to-the-resource-manager-template)
