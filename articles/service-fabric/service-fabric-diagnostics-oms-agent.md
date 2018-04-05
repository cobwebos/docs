---
title: Azure Service Fabric - 使用 OMS 代理设置监视 | Microsoft Docs
description: 了解如何设置 OMS 代理以监视 Azure Service Fabric 群集的容器和性能计数器。
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/20/2018
ms.author: dekapur
ms.openlocfilehash: 4b0845cbb25d160b53b483641e242422c98029ee
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
# <a name="add-the-oms-agent-to-a-cluster"></a>将 OMS 代理添加到群集

本文介绍如何逐步将 OMS 代理作为虚拟机规模集扩展添加到群集并将其连接到现有的 Azure Log Analytics 工作区。 这可收集关于容器、应用程序和性能监视的诊断数据。 通过将其添加为扩展，Azure 资源管理器可确保它安装在每个节点上，即使在缩放群集时也是如此。

> [!NOTE]
> 本文假定已设置了 Azure Log Analytics 工作区。 如果尚未设置，请转到[设置 Azure Log Analytics](service-fabric-diagnostics-oms-setup.md)

## <a name="add-the-agent-extension-via-azure-cli"></a>通过 Azure CLI 添加代理扩展

将 OMS 代理添加到群集的最佳方法是使用 Azure CLI 提供的虚拟机规模集 API。 如果尚未设置 Azure CLI，请转至 Azure 门户并打开 [Cloud Shell](../cloud-shell/overview.md) 实例，或[安装 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)。

1. 请求 Cloud Shell 之后，请确保在资源所在的订阅中工作。 请使用 `az account show` 进行检查，并确保“名称”值与群集订阅的值匹配。

2. 在门户中，导航到 Log Analytics 工作区所在的资源组。 在右侧导航栏中，单击进入 Log Analytics 资源（资源类型为 Log Analytics），向下滚动并单击“属性”。

    ![Log Analytics 属性页](media/service-fabric-diagnostics-oms-agent/oms-properties.png)

    记下 `workspaceId`。 

3. 还需要 `workspaceKey` 来部署代理。 要获取密钥，请单击左侧导航栏的“设置”部分下的“高级设置”。 若要建立 Windows 群集，请单击“Windows 服务器”；若要创建 Linux 群集，请单击“Linux 服务器”。 将需要“主键”作为 `workspaceKey`，显示该主键用于部署代理。

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
 
5. 运行命令将此配置应用于已存在的 VM 实例：  

    ```sh
    az vmss update-instances
    ```

    15 分钟内即可将代理成功添加到节点上。 可使用 `az vmss extension list` API 验证是否已添加代理：

    ```sh
    az vmss extension list --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType>
    ```

## <a name="add-the-agent-via-the-resource-manager-template"></a>通过资源管理器模板添加代理

部署 Azure Log Analytics 工作区并将代理添加到每个节点的示例资源管理器模板可用于 [Windows](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Windows) 或 [Linux](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux)。

可下载和修改此模板以部署最适合需求的群集。

## <a name="next-steps"></a>后续步骤

* 收集相关[性能计数器](service-fabric-diagnostics-event-generation-perf.md)。 若要配置 OMS 代理以收集特定性能计数器，请查看[配置数据源](../log-analytics/log-analytics-data-sources.md#configuring-data-sources)。
* 配置 Log Analytics，设置[自动警报](../log-analytics/log-analytics-alerts.md)，以辅助检测和诊断
