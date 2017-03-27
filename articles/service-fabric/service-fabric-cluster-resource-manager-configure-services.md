---
title: "指定 Azure 微服务中的指标和放置设置 | Microsoft Docs"
description: "通过指定指标、放置约束和其他放置策略来描述 Service Fabric 服务。"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 16e135c1-a00a-4c6f-9302-6651a090571a
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: bb27d279396aa7b670187560cebe2ed074576bad
ms.openlocfilehash: c1df3b77f3fa621a60d6ab73f6dc2c24abbc3366


---
# <a name="configuring-cluster-resource-manager-settings-for-service-fabric-services"></a>配置 Service Fabric 服务的群集资源管理器设置
使用 Service Fabric 群集资源管理器可以精细地控制用于管控每个命名服务的规则。 每个命名的服务实例可以指定其在群集中的分配方式的规则。 每个命名服务还可以定义要报告的指标集，包括指标集对该服务的重要程度。 配置服务的过程分为&3; 个不同的任务：

1. 配置放置约束
2. 配置指标
3. 配置高级放置策略和其他规则（不太常见）

## <a name="placement-constraints"></a>放置约束
放置约束可用来控制服务实际可在群集中的哪些节点上运行。 通常为特定的命名服务实例或受限于在特定类型节点上运行的类型的所有服务。 也就是说，放置约束是可扩展的 - 可以根据节点类型定义任意的属性组合，然后在创建服务时，利用约束来选择它们。 放置约束还会在服务生存期内动态更新，让你响应群集中的更改。 还可以在群集中动态更新给定节点的属性。 有关放置约束以及如何对其进行配置的详细信息，请参阅[此文](service-fabric-cluster-resource-manager-cluster-description.md#placement-constraints-and-node-properties)

## <a name="metrics"></a>度量值
指标是给定命名服务实例需要的资源组。 服务的指标配置包括默认情况下，该服务的每个有状态副本或无状态实例消耗多少资源。 指标还包括一个权重，表示均衡该指标对于服务的重要程度，以便于权衡利弊。

## <a name="other-placement-rules"></a>其他放置规则
还有其他类型的放置规则，用于地理分散的群集或其他较少见的方案。 其他放置规则通过关联或策略来配置。

## <a name="next-steps"></a>后续步骤
* 指标是 Service Fabric 群集资源管理器在群集中管理消耗和容量的方式。 若要详细了解指标及其配置方式，请查看[此文](service-fabric-cluster-resource-manager-metrics.md)
* 相关性是可以针对服务配置的一种模式。 它并不常用，但如果需要，可以参阅[此处](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
* 可以在服务上配置许多不同的放置规则以处理其他方案。 可以在[此处](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)了解这些不同的放置策略
* 从头开始并[获取 Service Fabric 群集资源管理器简介](service-fabric-cluster-resource-manager-introduction.md)
* 若要了解群集资源管理器如何管理和均衡群集中的负载，请查看有关[平衡负载](service-fabric-cluster-resource-manager-balancing.md)的文章
* 群集资源管理器提供许多用于描述群集的选项。 若要详细了解这些选项，请查看这篇[介绍 Service Fabric 群集](service-fabric-cluster-resource-manager-cluster-description.md)的文章



<!--HONumber=Jan17_HO4-->


