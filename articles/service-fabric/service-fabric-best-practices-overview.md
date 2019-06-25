---
title: Azure Service Fabric 应用程序和群集最佳做法 | Microsoft Docs
description: 用于管理 Service Fabric 群集和应用程序的最佳做法。
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/18/2019
ms.author: pepogors
ms.openlocfilehash: 5fdbd3f15b11e4c3975ca29627d5984382bcf049
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206799"
---
# <a name="azure-service-fabric-application-and-cluster-best-practices"></a>Azure Service Fabric 应用程序和群集最佳做法

本文提供了用于管理 Azure Service Fabric 应用程序和群集的最佳做法的链接。 我们强烈建议您实现这些做法来优化你的生产环境的可靠性。 使用之一[Service Fabric 群集模板](https://github.com/Azure-Samples/service-fabric-cluster-templates)开始设计你的生产解决方案，或更新现有模板以合并这些做法。

## <a name="security"></a>安全

* [安全性最佳做法](service-fabric-best-practices-security.md)

## <a name="networking"></a>网络

* [网络最佳做法](service-fabric-best-practices-networking.md)

## <a name="compute-planning-and-scaling"></a>计算规划和缩放

* [计算缩放最佳做法](service-fabric-best-practices-capacity-scaling.md)
* [计算容量规划](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)

## <a name="infrastructure-as-code"></a>基础结构即代码

* [用于实现基础结构即代码的最佳做法](service-fabric-best-practices-infrastructure-as-code.md)

## <a name="monitoring-and-diagnostics"></a>监视和诊断

* [群集监视和诊断最佳做法](service-fabric-best-practices-monitoring.md)

## <a name="application-design"></a>应用程序设计

* [应用程序设计最佳实践](service-fabric-best-practices-applications.md)

## <a name="checklist"></a>清单

在实现前面的部分中建议的做法后，请确保整合了生产就绪状态检查表中的所有最佳实践：
* [Azure Service Fabric 生产就绪清单](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist)

## <a name="next-steps"></a>后续步骤

* 在运行 Windows Server 的 VM 或计算机上创建群集：[创建适用于 Windows Server 的 Service Fabric 群集](service-fabric-cluster-creation-for-windows-server.md)
* 在运行 Linux 的 VM 或计算机上创建群集：[创建 Linux 群集](service-fabric-cluster-creation-via-portal.md)
* 对 Service Fabric 进行故障排除：[故障排除指南](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)