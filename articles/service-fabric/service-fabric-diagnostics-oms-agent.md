---
title: "Azure Service Fabric - 使用 OMS 代理设置监视 | Microsoft Docs"
description: "了解如何设置 OMS 代理以监视 Azure Service Fabric 群集的容器和性能计数器。"
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/31/2017
ms.author: dekapur
ms.openlocfilehash: 095db20e7d22bd517337f24fc9a81b84988d1465
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2018
---
# <a name="add-the-oms-agent-to-a-cluster"></a>将 OMS 代理添加到群集

本文介绍如何逐步将 OMS 代理作为虚拟机规模集扩展添加到群集并将其连接到现有的 OMS Log Analytics 工作区。 这可收集关于容器、应用程序和性能监视的诊断数据。 通过将其添加为扩展，Azure 资源管理器可确保它安装在每个节点上，即使在缩放群集时也是如此。

> [!NOTE]
> 本文假定已设置了 OMS Log Analytics 工作区。 如果尚未设置，请转到[设置 OMS Log Analytics](service-fabric-diagnostics-oms-setup.md)

## <a name="add-the-agent-extension-via-azure-cli"></a>通过 Azure CLI 添加代理扩展

将 OMS 代理添加到群集的最佳方法是使用 Azure CLI 提供的虚拟机规模集 API。 如果尚未设置 Azure CLI，请转至 Azure 门户并打开 [Cloud Shell](../cloud-shell/overview.md) 实例，或[安装 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)。

1. 请求 Cloud Shell 之后，请确保在资源所在的订阅中工作。 请使用 `az account show` 进行检查，并确保“名称”值与群集订阅的值匹配。

2. 在门户中，导航到 OMS 工作区所在的资源组。 在右侧导航栏中，单击进入 Log Analytics 资源（资源类型为 Log Analytics），向下滚动并单击“属性”。

    ![OMS 属性页面](media/service-fabric-diagnostics-oms-agent/oms-properties.png)

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
 
    15 分钟内即可将代理成功添加到节点上。 可使用 `az vmss extension list` API 验证是否已添加代理：

    ```sh
    az vmss extension list --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType>
    ```

## <a name="add-the-agent-via-the-resource-manager-template"></a>通过资源管理器模板添加代理

示例资源管理器模板面向 [Windows](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Windows) 或 [Linux](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux)可用于部署 OMS Log Analytics 工作区并将代理添加到每个节点。

可下载和修改此模板以部署最适合需求的群集。

## <a name="next-steps"></a>后续步骤

* 收集相关[性能计数器](service-fabric-diagnostics-event-generation-perf.md)。 若要配置 OMS 代理以获取特定的性能计数器，请转到 OMS 门户（链接在 OMS Log Analytics 资源的顶部）。 然后依次单击“主页”、“设置”、“数据”和“Windows 性能计数器”（或“Linux 性能计数器”），然后选择想要收集的计数器。
* 配置 OMS，设置[自动警报](../log-analytics/log-analytics-alerts.md)，以辅助检测和诊断
