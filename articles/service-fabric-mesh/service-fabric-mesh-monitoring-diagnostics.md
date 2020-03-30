---
title: Azure 服务结构网格应用中的监视和诊断
description: 了解在 Azure Service Fabric 网格中如何监控和诊断应用程序。
author: srrengar
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: srrengar
ms.custom: mvc, devcenter
ms.openlocfilehash: 247a1de4d00668371337295616d31caf101f0cc5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
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
> 可使用“az mesh service-replica”命令获取副本名称。 副本名称从 0 递增整数。

以下是用于查看投票应用程序中 VotingWeb.Code 容器的日志的示例：

```cli
az mesh code-package-log get --resource-group <nameOfRG> --application-name SbzVoting --service-name VotingWeb --replica-name 0 --code-package-name VotingWeb.Code
```

## <a name="container-metrics"></a>容器指标 

网格环境公开了一些指标，指示容器的性能。 以下指标可通过 Azure 门户和 Azure 监视器 CLI 获得：

| 指标 | 描述 | 单位|
|----|----|----|
| CpuUtilization | 实际 Cpu/分配 Cpu 以百分比表示 | % |
| MemoryUtilization | 实际 Mem/已分配 Mem 的百分比 | % |
| AllocatedCpu | 根据 Azure 资源管理器模板分配的 Cpu | 米利科茨 |
| AllocatedMemory | 根据 Azure 资源管理器模板分配的内存 | MB |
| ActualCpu | CPU 使用率 | 米利科茨 |
| ActualMemory | 内存使用率 | MB |
| ContainerStatus | 0 - 无效：容器状态未知 <br> 1 - 挂起：容器已计划启动 <br> 2 - 启动：容器正在启动中 <br> 3 - 已启动：容器已成功启动 <br> 4 - 停止：容器正在停止 <br> 5 - 已停止：容器已成功停止 | 空值 |
| ApplicationStatus | 0 - 未知：状态不可检索 <br> 1 - 就绪：应用程序已成功运行 <br> 2 - 升级：正在进行升级 <br> 3 - 创建：正在创建应用程序 <br> 4 - 删除：正在删除应用程序 <br> 5 - 失败：应用程序无法部署 | 空值 |
| ServiceStatus | 0 - 无效：该服务当前没有运行状况 <br> 1 - 确定：服务正常  <br> 2 - 警告：可能有问题需要调查 <br> 3 - 错误：有一些问题需要调查 <br> 4 - 未知：状态不可检索 | 空值 |
| ServiceReplicaStatus | 0 - 无效：副本当前没有运行状况 <br> 1 - 确定：服务正常  <br> 2 - 警告：可能有问题需要调查 <br> 3 - 错误：有一些问题需要调查 <br> 4 - 未知：状态不可检索 | 空值 | 
| RestartCount | 重新启动的容器数 | 空值 |

> [!NOTE]
> 服务状态和服务副本状态值与服务结构中的[运行状况状态](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet)相同。 

每个指标都在不同的维度上可用，因此您可以看到不同级别的聚合。 当前维度列表如下：

* ApplicationName
* ServiceName
* 服务副本名称
* 代码包名称

> [!NOTE]
> 代码包名称维度不适用于 Linux 应用程序。 

每个维度对应于[服务结构应用程序模型](service-fabric-mesh-service-fabric-resources.md#applications-and-services)的不同组件

### <a name="azure-monitor-cli"></a>Azure 监视器 CLI

[Azure 监视器 CLI 文档中](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list)提供了完整的命令列表，但我们在下面提供了一些有用的示例 

在每个示例中，资源 ID 遵循此模式

`"/subscriptions/<your sub ID>/resourcegroups/<your RG>/providers/Microsoft.ServiceFabricMesh/applications/<your App name>"`


* 应用程序中容器的 CPU 利用率

```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization"
```
* 每个服务副本的内存利用率
```cli
    az monitor metrics list --resource <resourceId> --metric "MemoryUtilization" --dimension "ServiceReplicaName"
``` 

* 在 1 小时窗口中重新启动每个容器 
```cli
    az monitor metrics list --resource <resourceId> --metric "RestartCount" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z
``` 

* 在 1 小时窗口中，名为"投票 Web"的服务的平均 CPU 利用率
```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z --aggregation "Average" --filter "ServiceName eq 'VotingWeb'"
``` 

### <a name="metrics-explorer"></a>指标资源管理器

指标资源管理器是门户中的边栏选项卡，您可以在其中可视化网格应用程序的所有指标。 此边栏选项卡可在门户中的应用程序页面和 Azure 监视器边栏选项卡中访问，后者可用于查看支持 Azure 监视器的所有 Azure 资源的指标。 

![指标资源管理器](./media/service-fabric-mesh-monitoring-diagnostics/metricsexplorer.png)


<!--
### Container Insights

In addition to the metrics explorer, we also have a dashboard available out of the box that shows sample metrics over time under the Insights blade in the application's page in the portal. 

![Container Insights](./media/service-fabric-mesh-monitoring-diagnostics/containerinsights.png)
-->

## <a name="next-steps"></a>后续步骤
* 若要详细了解 Service Fabric 网格，请阅读 [Service Fabric 网格概述](service-fabric-mesh-overview.md)。
* 要了解有关 Azure 监视器指标命令的更多详细信息，请查看[Azure 监视器 CLI 文档](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list)。
