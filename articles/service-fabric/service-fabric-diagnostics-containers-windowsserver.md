---
title: "Azure Service Fabric 容器监视和诊断 | Microsoft Docs"
description: "了解如何使用 OMS 的容器解决方案监视和诊断安排在 Microsoft Azure Service Fabric 上的容器。"
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
ms.date: 05/10/2017
ms.author: dekapur
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 874c1a5c4b399ff2254072b7282f05d83a005cc3
ms.contentlocale: zh-cn
ms.lasthandoff: 05/11/2017

---

# <a name="monitoring-windows-server-containers-with-oms"></a>使用 OMS 监视 Windows Server 容器

## <a name="oms-containers-solution"></a>OMS 容器解决方案

Operations Management Suite (OMS) 团队已经发布了用于诊断和监视容器的解决方案。 除了 Service Fabric 解决方案，该解决方案也是用于监视 Service Fabric 上安排的容器部署的绝佳工具。 以下示例简单展示了解决方案中仪表板的外观：

![基本 OMS 仪表板](./media/service-fabric-diagnostics-containers-windowsserver/oms-containers-dashboard.png)

它还收集可在 OMS Log Analytics 工具中查询的不同类型的日志，并能够可视化生成的任何指标或事件。 收集的日志类型：

1. ContainerInventory：显示有关容器位置、名称和图像的信息
2. ContainerImageInventory：有关已部署映像的信息，包括 ID 或大小
3. ContainerLog：特定的错误日志、stdout 等 docker 日志和其他条目
4. ContainerServiceLog：已运行的 docker 守护程序命令
5. Perf：性能计数器，包括容器 cpu、内存、网络流量、磁盘 i/o 和主机的自定义指标

本文介绍如何为群集设置容器监视。 若要了解有关 OMS 容器解决方案的详细信息，请查看相关[文档](../log-analytics/log-analytics-containers.md)。

## <a name="1-set-up-a-service-fabric-cluster"></a>1.设置 Service Fabric 群集

使用[此处](https://github.com/dkkapur/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Sample)的 Azure Resource Manager 模板创建群集。 确保添加唯一的 OMS 工作区名称。 此模板还默认在 Service Fabric (v255.255) 的预览版中部署群集，这意味着它不能用于生产，且无法升级到其他 Service Fabric 版本。 如果要长期使用此模板或要将其用于生产，请将版本更改为稳定的版本。

群集设置完成后，请确认是否已安装相应证书，并确保能够连接到群集。

前往 [Azure 门户](https://portal.azure.com/)并查找部署，确认已正确设置资源组。 资源组应包含所有 Service Fabric 资源，以及 Log Analytics 解决方案和 Service Fabric 解决方案。

修改现有 Service Fabric 群集：
* 确认已启用“诊断”（如未启用，请通过[更新虚拟机规模集](/rest/api/virtualmachinescalesets/create-or-update-a-set)启用）
* 通过经由 Azure Marketplace 创建“Service Fabric 分析”解决方案，添加 OMS 工作区
* 编辑 Service Fabric 解决方案的数据源，从群集所在的资源组中的相应 Azure存储表（由 WAD 设置）中提取数据
* 通过 PowerShell 或通过更新虚拟机规模集（与上述链接相同，用于修改 Resource Manager 模板）将代理作为[扩展添加到虚拟机规模集](/powershell/module/azurerm.compute/add-azurermvmssextension)

## <a name="2-deploy-a-container"></a>2.部署容器

一旦群集准备就绪，且已确认可访问群集，即可将容器部署到群集。 如果选择使用模板设置的预览版，还可探索 Service Fabric 新的 Docker Compose 功能。 请记住，首次将容器映像部署到群集时，需要几分钟才能下载映像，具体时间取决于映像大小。

## <a name="3-add-the-containers-solution"></a>3.添加容器解决方案

在 Azure 门户中，通过 Azure Marketplace 创建容器资源（在“监视和管理”类别下）。 

![添加容器解决方案](./media/service-fabric-diagnostics-containers-windowsserver/containers-solution.png)

创建过程中，需使用 OMS 工作区。 选择使用上述部署创建的工作区。 此步骤在 OMS 工作区中添加容器解决方案，并由模板部署的 OMS 代理进行自动检测。 代理开始在群集中收集容器进程的数据，大约 10-15 分钟后，即可看到带数据的解决方案亮起，如上方仪表板图像所示。

## <a name="4-next-steps"></a>4.后续步骤

OMS 在工作区提供各种工具，为用户提供更多帮助。 了解以下选项，按需自定义解决方案：
- 掌握 Log Analytics 中提供的[日志搜索和查询](../log-analytics/log-analytics-log-searches.md)功能
- 配置 OMS 代理，获取特定性能计数器（转到工作区“主页”>“设置”>“数据”>“ Windows 性能计数器”）
- 配置 OMS，设置[自动警报](../log-analytics/log-analytics-alerts.md)规则，以辅助检测和诊断
