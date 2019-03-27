---
title: 规划 Azure Service Fabric 群集部署 |Microsoft Docs
description: 了解如何规划和准备用于生产 Service Fabric 群集部署到 Azure。
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: aljo
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/20/2019
ms.author: ryanwi
ms.openlocfilehash: d29f9af4201eee41720fe60cc9b7a21abc7fc63d
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/26/2019
ms.locfileid: "58449913"
---
# <a name="plan-and-prepare-for-a-cluster-deployment"></a>规划和准备群集部署

规划和准备对于生产群集部署是非常重要。  有许多需要考虑的因素。  这篇文章将指导你完成准备群集部署的步骤。

## <a name="read-the-best-practices-information"></a>读取的最佳实践信息
若要成功管理 Azure Service Fabric 应用程序和群集，有一些操作，我们强烈建议您执行以优化你的生产环境的可靠性。  有关详细信息，请阅读[Service Fabric 应用程序和群集的最佳做法](service-fabric-best-practices-overview.md)。

## <a name="select-the-os-for-the-cluster"></a>选择群集操作系统
使用 Service Fabric 可在运行 Windows Server 或 Linux 的任何 VM 或计算机上创建 Service Fabric 群集。  在部署之前你的群集，必须选择操作系统：Windows 或 Linux。  在群集中的每个节点 （虚拟机） 运行相同的操作系统，不能在同一群集中混合 Windows 和 Linux Vm。

## <a name="capacity-planning"></a>容量计划
对于任何生产部署，容量规划都是一个重要的步骤。 下面是在规划过程中必须注意的一些事项。

* 群集的节点类型的初始数目 
* 每个节点类型 （大小、 数量的实例，主要、 面向 internet、 Vm 数目，等等） 的属性
* 群集的可靠性和耐久性特征

### <a name="select-the-initial-number-of-node-types"></a>选择节点类型的初始的数目
首先，需要确定要创建的群集用于什么目的， 以及打算要将哪些类型的应用程序部署到此群集中。 应用程序是否有多个服务，其中是否有任何服务需是面向公众或面向 Internet 的服务？ （构成应用程序）的服务是否有不同的基础结构要求，例如，更多的 RAM 或更高的 CPU 周期？ Service Fabric 群集可包含多个节点类型： 主节点类型和一个或多个非主节点类型。 每个节点类型将映射到虚拟机规模集。 然后，每个节点类型可以独立扩展或缩减、打开不同的端口集，并可以有不同的容量指标。 [节点属性和放置约束][ placementconstraints]可以设置以限制到特定节点类型的特定服务。  有关详细信息，请阅读[群集需要一开始的节点类型数目](service-fabric-cluster-capacity.md#the-number-of-node-types-your-cluster-needs-to-start-out-with)。

### <a name="select-node-properties-for-each-node-type"></a>选择每个节点类型的节点属性
节点类型定义相关联的规模集中的 VM SKU、 数字和 Vm 的属性。

每个节点类型的 Vm 的最小大小由[耐久性层][ durability]为节点类型选择。

由主节点类型的 Vm 数目下限[可靠性层][ reliability]选择。

请参阅有关的最小建议[主节点类型](service-fabric-cluster-capacity.md#primary-node-type---capacity-guidance)，[非主节点类型上的有状态工作负荷](service-fabric-cluster-capacity.md#non-primary-node-type---capacity-guidance-for-stateful-workloads)，并[非主节点类型上的无状态工作负荷](service-fabric-cluster-capacity.md#non-primary-node-type---capacity-guidance-for-stateless-workloads)。 

任何多个节点的最小数目应该基于你想要在此节点类型中运行的应用程序/服务的副本数。  [Service Fabric 应用程序的容量规划](service-fabric-capacity-planning.md)可帮助您估计需要运行你的应用程序的资源。 您始终可以纵向扩展群集，或向下更高版本以进行调整的更改应用程序工作负荷。 

### <a name="select-the-durability-and-reliability-levels-for-the-cluster"></a>选择群集的持续性和可靠性级别
耐久性层用于向系统指示 VM 对于基本 Azure 基础结构拥有的权限。 在主节点类型中，此权限可让 Service Fabric 暂停影响系统服务及有状态服务的仲裁要求的任何 VM 级别基础结构请求（例如，VM 重新启动、VM 重置映像或 VM 迁移）。 在非主节点类型中，此特权可让 Service Fabric 暂停影响其中运行的有状态服务的仲裁要求的任何 VM 级别基础结构请求，例如，VM 重新启动、VM 重置映像、VM 迁移，等等。  有关不同级别和要使用和时，请参阅的级别的建议的优点[群集的持久性特征][durability]。

可靠性层用于设置要在此群集中的主节点类型上运行的系统服务副本数。 副本数越大，群集中的系统服务越可靠。  有关不同级别和要使用和时，请参阅的级别的建议的优点[群集的可靠性特征][reliability]。 

## <a name="enable-reverse-proxy-andor-dns"></a>启用反向代理和/或 DNS
在群集内相互连接的服务通常可以直接访问其他服务的终结点，因为群集中的节点处于相同的本地网络上。 若要使服务之间连接更轻松，Service Fabric 提供其他服务：一个[DNS 服务](service-fabric-dnsservice.md)和一个[反向代理服务](service-fabric-reverseproxy.md)。  部署群集时，可以启用这两个服务。

由于许多服务，尤其是容器化的服务，可以有一个现有的 URL 名称，能够解析这些使用标准 DNS 协议 （而不是命名服务协议） 是方便，尤其是在应用程序"提升和转移"方案。 这正是 DNS 服务能够发挥作用的地方。 借助 DNS 服务，用户能够将 DNS 名称映射到服务名称，进而解析终结点 IP 地址。

反向代理处理群集中公开 HTTP 终结点 （包括 HTTPS） 的服务。 反向代理极大地简化调用其他服务通过提供特定的 URI 格式。  反向代理还处理解析、 连接和重试步骤所需的一项服务与另一个进行通信。

## <a name="prepare-for-disaster-recovery"></a>为灾难恢复做准备
提供高可用性的关键一环是确保服务能够经受各种不同类型的故障。 对于计划外和不受控制的故障，这一点尤其重要。 [准备灾难恢复](service-fabric-disaster-recovery.md)介绍一些常见的故障模式，如果未正确建模和管理可能成为灾难。 它还讨论了缓解措施和发生灾难时要采取的操作。

## <a name="production-readiness-checklist"></a>生产就绪情况核对清单
应用程序和群集是否准备好接收生产流量？ 在你的群集部署到生产环境之前, 运行[生产就绪清单](service-fabric-production-readiness-checklist.md)。 确保您的应用程序和群集通过完成此清单中的项平稳运行。 我们强烈建议所有这些项目以在投入生产之前检查。

## <a name="next-steps"></a>后续步骤
* [创建运行 Windows 的 Service Fabric 群集](service-fabric-best-practices-overview.md)
* [创建运行 Linux 的 Service Fabric 群集](service-fabric-tutorial-create-vnet-and-linux-cluster.md)

[placementconstraints]: service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints
[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster