---
title: Azure Service Fabric - 使用 Log Analytics 设置性能监视 | Microsoft Docs
description: 了解如何设置 OMS 代理以监视 Azure Service Fabric 群集的容器和性能计数器。
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/16/2018
ms.author: dekapur; srrengar
ms.openlocfilehash: 6e1c870458f43bcc5d6d40f0e40e2b2a95bee2af
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2018
---
# <a name="performance-monitoring-with-log-analytics"></a>使用 Log Analytics 设置性能监视

本文介绍如何逐步将 Log Analytics（也称为 OMS）代理作为虚拟机规模集扩展添加到群集并将其连接到现有的 Azure Log Analytics 工作区。 这可收集关于容器、应用程序和性能监视的诊断数据。 通过将其作为扩展添加到虚拟机规模集资源，Azure 资源管理器可确保它安装在每个节点上，即使在缩放群集时也是如此。

> [!NOTE]
> 本文假定已设置了 Azure Log Analytics 工作区。 如果尚未设置，请转到[设置 Azure Log Analytics](service-fabric-diagnostics-oms-setup.md)

## <a name="add-the-agent-extension-via-azure-cli"></a>通过 Azure CLI 添加代理扩展

将 OMS 代理添加到群集的最佳方法是使用 Azure CLI 提供的虚拟机规模集 API。 如果尚未设置 Azure CLI，请转至 Azure 门户并打开 [Cloud Shell](../cloud-shell/overview.md) 实例，或[安装 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)。

1. 请求 Cloud Shell 之后，请确保在资源所在的订阅中工作。 请使用 `az account show` 进行检查，并确保“名称”值与群集订阅的值匹配。

2. 在门户中，导航到 Log Analytics 工作区所在的资源组。 单击进入 Log Analytics 资源（资源类型为 Log Analytics）。 进入资源概述页后，单击左侧菜单中“设置”部分下面的“高级设置”。

    ![Log Analytics 属性页](media/service-fabric-diagnostics-oms-agent/oms-advanced-settings.png)
 
3. 若要建立 Windows 群集，请单击“Windows 服务器”；若要创建 Linux 群集，请单击“Linux 服务器”。 此页将显示 `workspace ID` 和 `workspace key`（在门户中列为“主键”）。 下一步骤需要使用这两个值。

4. 使用 Cloud Shell 中的 `vmss extension set` API 运行命令，将 OMS 代理安装到群集中：

    对于 Windows 群集：
    
    ```sh
    az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<OMSworkspaceId>'}" --protected-settings "{'workspaceKey':'<OMSworkspaceKey>'}"
    ```

    对于 Linux 群集：

    ```sh
    az vmss extension set --name OmsAgentForLinux --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<OMSworkspaceId>'}" --protected-settings "{'workspaceKey':'<OMSworkspaceKey>'}"
    ```

    以下示例展示正在添加到 Windows 群集的 OMS 代理。

    ![OMS 代理 cli 命令](media/service-fabric-diagnostics-oms-agent/cli-command.png)
 
5. 15 分钟内即可将代理成功添加到节点上。 可使用 `az vmss extension list` API 验证是否已添加代理：

    ```sh
    az vmss extension list --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType>
    ```

## <a name="add-the-agent-via-the-resource-manager-template"></a>通过资源管理器模板添加代理

部署 Azure Log Analytics 工作区并将代理添加到每个节点的示例资源管理器模板可用于 [Windows](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Windows) 或 [Linux](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux)。

可下载和修改此模板以部署最适合需求的群集。

## <a name="view-performance-counters-in-the-log-analytics-portal"></a>在 Log Analytics 门户中查看性能计数器

添加 OMS 代理后，请转到 Log Analytics 门户，选择要收集的性能计数器。 

1. 在 Azure 门户中，转到在其中创建 Service Fabric 分析解决方案的资源组。 选择 ServiceFabric\<nameOfOMSWorkspace\>然后转到其概述页。 单击顶部的链接转到 OMS 门户。

2. 进入门户后，将在图形窗体中看到每个已启用的解决方案的磁贴，包括 Service Fabric 的磁贴。 单击磁贴可转到 Service Fabric 分析解决方案。 

3. 随后将会看到一些磁贴，以及有关操作通道和 Reliable Services 事件的图形。 在右侧单击齿轮图标转到设置页。

    ![OMS 设置](media/service-fabric-diagnostics-oms-agent/oms-solutions-settings.png)

4. 在设置页上，单击“数据”并选择“Windows 或 Linux 性能计数器”。 此时会显示一个可以选择的默认选项列表，另外还可以设置收集间隔。 还可以添加要收集的[其他性能计数器](service-fabric-diagnostics-event-generation-perf.md)。 此[参考文章](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85).aspx)中介绍了正确的格式。

配置计数器后，请返回解决方案页，随即会看到数据流入并显示在“节点指标”下面的图形中。 还可以使用 Kusto 查询语言，像查询群集事件一样查询性能计数器数据，以及基于节点、性能计数器名称和值进行筛选。 

![OMS 性能计数器查询](media/service-fabric-diagnostics-oms-agent/oms-perf-counter-query.png)

## <a name="next-steps"></a>后续步骤

* 收集相关[性能计数器](service-fabric-diagnostics-event-generation-perf.md)。 若要配置 OMS 代理以收集特定性能计数器，请查看[配置数据源](../log-analytics/log-analytics-data-sources.md#configuring-data-sources)。
* 配置 Log Analytics，设置[自动警报](../log-analytics/log-analytics-alerts.md)，以辅助检测和诊断
* 作为替代方法，可以通过 [Azure 诊断扩展收集性能计数器并将其发送到 Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md#add-the-ai-sink-to-the-resource-manager-template)