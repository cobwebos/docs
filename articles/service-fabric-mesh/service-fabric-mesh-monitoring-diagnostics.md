---
title: Azure Service Fabric 网格应用中的监视和诊断
description: 了解在 Azure Service Fabric 网格中如何监控和诊断应用程序。
author: srrengar
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: srrengar
ms.custom: mvc, devcenter
ms.openlocfilehash: 247a1de4d00668371337295616d31caf101f0cc5
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/26/2019
ms.locfileid: "75498144"
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
> 可使用“az mesh service-replica”命令获取副本名称。 副本名称是从0递增的整数。

以下是用于查看投票应用程序中 VotingWeb.Code 容器的日志的示例：

```cli
az mesh code-package-log get --resource-group <nameOfRG> --application-name SbzVoting --service-name VotingWeb --replica-name 0 --code-package-name VotingWeb.Code
```

## <a name="container-metrics"></a>容器指标 

网格环境公开了一些指标，用于指示容器的执行方式。 以下指标可通过 Azure 门户和 Azure monitor CLI 获得：

| 度量值 | Description | 单位|
|----|----|----|
| CpuUtilization | ActualCpu/AllocatedCpu 作为百分比 | % |
| MemoryUtilization | ActualMem/AllocatedMem 作为百分比 | % |
| AllocatedCpu | 按 Azure 资源管理器模板分配的 Cpu | Millicores |
| AllocatedMemory | 按 Azure 资源管理器模板分配的内存 | MB |
| ActualCpu | CPU 使用率 | Millicores |
| ActualMemory | 内存使用率 | MB |
| ContainerStatus | 0-无效：容器状态未知 <br> 1-挂起：容器已计划启动 <br> 2-正在启动：容器处于启动过程中 <br> 3-已启动：容器已成功启动 <br> 4-正在停止：正在停止容器 <br> 5-已停止：容器已成功停止 | N/A |
| ApplicationStatus | 0-未知：状态不可检索 <br> 1-准备就绪：应用程序正在成功运行 <br> 2-正在升级：正在进行升级 <br> 3-正在创建：正在创建应用程序 <br> 4-正在删除：正在删除应用程序 <br> 5-失败：应用程序部署失败 | N/A |
| ServiceStatus | 0-无效：服务当前没有运行状况状态 <br> 1-正常：服务正常  <br> 2-警告：可能有问题需要调查 <br> 3-错误：需要调查的内容有误 <br> 4-未知：状态不可检索 | N/A |
| ServiceReplicaStatus | 0-无效：副本当前不具有运行状况状态 <br> 1-正常：服务正常  <br> 2-警告：可能有问题需要调查 <br> 3-错误：需要调查的内容有误 <br> 4-未知：状态不可检索 | N/A | 
| RestartCount | 容器重启次数 | N/A |

> [!NOTE]
> ServiceStatus 和 ServiceReplicaStatus 值与 Service Fabric 中的[HealthState](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet)相同。 

每个指标都可以在不同的维度上使用，因此可以在不同级别查看聚合。 维度的当前列表如下所示：

* ApplicationName
* ServiceName
* ServiceReplicaName
* CodePackageName

> [!NOTE]
> CodePackageName 维度不适用于 Linux 应用程序。 

每个维度对应于[Service Fabric 应用程序模型](service-fabric-mesh-service-fabric-resources.md#applications-and-services)的不同组件

### <a name="azure-monitor-cli"></a>Azure Monitor CLI

[AZURE MONITOR CLI 文档](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list)中提供了完整命令列表，但我们提供了以下几个有用的示例 

在每个示例中，资源 ID 遵循此模式

`"/subscriptions/<your sub ID>/resourcegroups/<your RG>/providers/Microsoft.ServiceFabricMesh/applications/<your App name>"`


* 应用程序中容器的 CPU 利用率

```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization"
```
* 每个服务副本的内存使用率
```cli
    az monitor metrics list --resource <resourceId> --metric "MemoryUtilization" --dimension "ServiceReplicaName"
``` 

* 在1小时内为每个容器重启 
```cli
    az monitor metrics list --resource <resourceId> --metric "RestartCount" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z
``` 

* 1小时窗口内名为 "VotingWeb" 的服务的平均 CPU 使用率
```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z --aggregation "Average" --filter "ServiceName eq 'VotingWeb'"
``` 

### <a name="metrics-explorer"></a>指标资源管理器

指标资源管理器是门户中的一个边栏选项卡，可以在其中可视化网格应用程序的所有指标。 此边栏选项卡可在门户和 Azure monitor 边栏选项卡的应用程序页面中访问，后者可用于查看支持 Azure Monitor 的所有 Azure 资源的指标。 

![指标资源管理器](./media/service-fabric-mesh-monitoring-diagnostics/metricsexplorer.png)


<!--
### Container Insights

In addition to the metrics explorer, we also have a dashboard available out of the box that shows sample metrics over time under the Insights blade in the application's page in the portal. 

![Container Insights](./media/service-fabric-mesh-monitoring-diagnostics/containerinsights.png)
-->

## <a name="next-steps"></a>后续步骤
* 若要详细了解 Service Fabric 网格，请阅读 [Service Fabric 网格概述](service-fabric-mesh-overview.md)。
* 若要详细了解 Azure Monitor 指标命令，请查看[AZURE MONITOR CLI 文档](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list)。
