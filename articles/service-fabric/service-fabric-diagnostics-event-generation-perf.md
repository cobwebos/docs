---
title: "Azure Service Fabric 性能监视 | Microsoft Docs"
description: "了解用于监视和诊断 Azure Service Fabric 群集的性能计数器。"
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
ms.date: 06/30/2017
ms.author: dekapur
ms.openlocfilehash: 9d63148c182c705b6b49733c59ed8fdd13872d72
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="performance-metrics"></a>性能指标

应收集指标，以了解群集及其中运行的应用程序的性能。 对于 Service Fabric 群集，建议收集以下性能计数器。

## <a name="nodes"></a>Nodes

对于群集中的计算机，建议收集以下性能计数器，以便更好地了解每台计算机上的负载，并制定相应的群集缩放决策。

| 计数器类别 | 计数器名称 |
| --- | --- |
| PhysicalDisk(per Disk) | 平均磁盘读取队列长度 |
| PhysicalDisk(per Disk) | 平均磁盘写入队列长度 |
| PhysicalDisk(per Disk) | 平均磁盘秒数/读取 |
| PhysicalDisk(per Disk) | 平均磁盘秒数/写入 |
| PhysicalDisk(per Disk) | 磁盘读取数/秒 |
| PhysicalDisk(per Disk) | 磁盘读取字节数/秒 |
| PhysicalDisk(per Disk) | 磁盘写入数/秒 |
| PhysicalDisk(per Disk) | 磁盘写入字节数/秒 |
| 内存 | 可用兆字节数 |
| PagingFile | 使用百分比 |
| Process(Total) | 处理器时间百分比 |
| Process (per service) | 处理器时间百分比 |
| Process (per service) | ID 进程 |
| Process (per service) | 专用字节 |
| Process (per service) | 线程计数 |
| Process (per service) | 虚拟字节 |
| Process (per service) | 工作集 |
| Process (per service) | 工作集 - 专用 |

## <a name="net-applications-and-services"></a>.NET 应用程序和服务

若要将 .NET 服务部署到群集，请收集以下计数器。 

| 计数器类别 | 计数器名称 |
| --- | --- |
| .NET CLR Memory (per service) | 进程 ID |
| .NET CLR Memory (per service) | 提交的字节总数 |
| .NET CLR Memory (per service) | 保留的字节总数 |
| .NET CLR Memory (per service) | 所有堆中的字节数 |
| .NET CLR Memory (per service) | 第 0 代集合数 |
| .NET CLR Memory (per service) | 第 1 代集合数 |
| .NET CLR Memory (per service) | 第 2 代集合数 |
| .NET CLR Memory (per service) | GC 中的时间百分比 |

### <a name="service-fabrics-custom-performance-counters"></a>Service Fabric 的自定义性能计数器

Service Fabric 生成大量自定义性能计数器。 如果已安装 SDK，可以在 Windows 计算机上的“性能监视器”应用程序（“开始”>“性能监视器”）中看到完整列表。 

在要将应用程序部署到的群集中，如果使用的是 Reliable Actors，请添加 `Service Fabric Actor` 和 `Service Fabric Actor Method` 类别的计数器（请参阅 [Service Fabric Reliable Actors 的诊断](service-fabric-reliable-actors-diagnostics.md)）。

如果使用 Reliable Services，同样可以添加 `Service Fabric Service` 和 `Service Fabric Service Method` 计数器类别来收集计数器。 

如果使用 Reliable Collections，建议通过 `Service Fabric Transactional Replicator` 添加 `Avg. Transaction ms/Commit`，以收集每个事务指标的平均提交延迟。


## <a name="next-steps"></a>后续步骤

* 详细了解 Service Fabric 中的[平台级事件生成情况](service-fabric-diagnostics-event-generation-infra.md)
* 通过 [Azure 诊断](service-fabric-diagnostics-event-aggregation-wad.md)收集性能指标
