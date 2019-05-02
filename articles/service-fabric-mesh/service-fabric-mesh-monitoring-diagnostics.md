---
title: Azure Service Fabric 网格应用程序中的监控和诊断 | Microsoft Docs
description: 了解在 Azure Service Fabric 网格中如何监控和诊断应用程序。
services: service-fabric-mesh
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2019
ms.author: srrengar
ms.custom: mvc, devcenter
ms.openlocfilehash: 36c9a5d75c4a72365638619ab85d451df647feb3
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2019
ms.locfileid: "64939827"
---
# <a name="monitoring-and-diagnostics"></a>监视和诊断
Azure Service Fabric 网格是一个完全托管的服务，由此开发者可部署微服务应用程序，而无需管理虚拟机、存储或网络。 Service Fabric 网格的监控和诊断分为三大类型诊断数据：

- 应用程序日志 - 这是基于应用程序检测方式的容器化应用程序的日志（如 docker 日志）
- 平台事件 - 网格平台中与容器操作相关的事件，当前包括容器激活、停用和终止。
- 容器指标 - 容器的资源利用率和性能指标（docker 统计数据）

本文介绍可用的最新预览版本的监视和诊断选项。

## <a name="application-logs"></a>应用程序日志

可基于每个容器，从已部署的容器查看 docker 日志。 在 Service Fabric 网格应用程序模型中，每个容器是应用程序中的代码包。 要查看代码包的相关日志，请使用以下命令：

```cli
az mesh code-package-log get --resource-group <nameOfRG> --app-name <nameOfApp> --service-name <nameOfService> --replica-name <nameOfReplica> --code-package-name <nameOfCodePackage>
```

> [!NOTE]
> 可使用“az mesh service-replica”命令获取副本名称。 副本名称递增从 0 的整数。

以下是用于查看投票应用程序中 VotingWeb.Code 容器的日志的示例：

```cli
az mesh code-package-log get --resource-group <nameOfRG> --application-name SbzVoting --service-name VotingWeb --replica-name 0 --code-package-name VotingWeb.Code
```

## <a name="container-metrics"></a>容器指标 

在网格环境公开少量的度量值，该值指示如何执行你的容器。 以下度量值是通过 Azure 门户和 Azure 监视器 CLI:

| 指标 | 描述 | 单位|
|----|----|----|
| CpuUtilization | ActualCpu/AllocatedCpu 百分比 | % |
| MemoryUtilization | ActualMem/AllocatedMem 百分比 | % |
| AllocatedCpu | Cpu 分配根据 Azure 资源管理器模板 | Millicores |
| AllocatedMemory | Azure 资源管理器模板根据分配的内存 | MB |
| ActualCpu | CPU 使用率 | Millicores |
| ActualMemory | 内存使用率 | MB |
| ContainerStatus | 0-无效：容器状态为未知 <br> 1-挂起：容器已计划开始 <br> 2-从开始：正在启动容器 <br> 3-启动：已成功启动容器 <br> 4-正在停止：正在停止容器 <br> 5-已停止：已成功停止容器 | 不适用 |
| ApplicationStatus | 0-未知：状态不是可检索 <br> 1-准备就绪：已成功运行该应用程序 <br> 2-升级：没有正在进行升级 <br> 3-创建：正在创建的应用程序 <br> 4-删除：正在删除应用程序 <br> 5-失败：应用程序部署失败 | 不适用 |
| ServiceStatus | 0-无效：该服务当前不具有运行状况状态 <br> 1-确定:该服务处于正常状态  <br> 2-警告：可能存在一些问题需要调查 <br> 3-错误：出现了问题，需要调查 <br> 4-未知：状态不是可检索 | 不适用 |
| ServiceReplicaStatus | 0-无效：副本当前不具有运行状况状态 <br> 1-确定:该服务处于正常状态  <br> 2-警告：可能存在一些问题需要调查 <br> 3-错误：出现了问题，需要调查 <br> 4-未知：状态不是可检索 | 不适用 | 
| RestartCount | 容器重启次数 | 不适用 |

> [!NOTE]
> ServiceStatus 和 ServiceReplicaStatus 值将与相同[HealthState](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet) Service Fabric 中。 

每个度量值是可在不同的维度上，因此您可以看到在不同级别的聚合。 维度的当前列表如下所示：

* ApplicationName
* ServiceName
* ServiceReplicaName
* CodePackageName

> [!NOTE]
> CodePackageName 维度不是适用于 Linux 应用程序。 

每个维度对应的不同组件[Service Fabric 应用程序模型](service-fabric-mesh-service-fabric-resources.md#applications-and-services)

### <a name="azure-monitor-cli"></a>Azure 监视器 CLI

命令的完整列表现已推出[Azure Monitor CLI docs](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list)但我们提供以下几个有用示例 

在每个示例中，资源 ID 采用这种模式

`"/subscriptions/<your sub ID>/resourcegroups/<your RG>/providers/Microsoft.ServiceFabricMesh/applications/<your App name>"`


* 应用程序中的容器的 CPU 使用率

```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization"
```
* 对于每个服务副本的内存使用率
```cli
    az monitor metrics list --resource <resourceId> --metric "MemoryUtilization" --dimension "ServiceReplicaName"
``` 

* 在 1 小时的窗口中每个容器的重启 
```cli
    az monitor metrics list --resource <resourceId> --metric "RestartCount" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z
``` 

* 在服务之间的平均 CPU 利用率名为"VotingWeb"1 小时窗口中
```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z --aggregation "Average" --filter "ServiceName eq 'VotingWeb'"
``` 

### <a name="metrics-explorer"></a>指标资源管理器

指标资源管理器是在其中可以所有度量值可视化网格应用程序在门户中的边栏选项卡。 在门户和 Azure monitor 边栏选项卡中，后者可用于在其中查看所有 Azure 资源支持 Azure Monitor 的指标中的应用程序页中访问此边栏选项卡。 

![指标资源管理器](./media/service-fabric-mesh-monitoring-diagnostics/metricsexplorer.png)


<!--
### Container Insights

In addition to the metrics explorer, we also have a dashboard available out of the box that shows sample metrics over time under the Insights blade in the application's page in the portal. 

![Container Insights](./media/service-fabric-mesh-monitoring-diagnostics/containerinsights.png)
-->

## <a name="next-steps"></a>后续步骤
* 若要详细了解 Service Fabric 网格，请阅读 [Service Fabric 网格概述](service-fabric-mesh-overview.md)。
* 若要了解有关 Azure Monitor 指标命令的详细信息，请查看[Azure Monitor CLI docs](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list)。
