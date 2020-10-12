---
title: Azure Service Fabric 应用程序和群集最佳做法
description: 使用 Azure Service Fabric 管理群集、应用和服务的最佳做法和设计注意事项。
author: peterpogorski
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: pepogors
ms.openlocfilehash: 86a02fd489ca0eec61b798db7136f963277f6c82
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86261086"
---
# <a name="azure-service-fabric-application-and-cluster-best-practices"></a>Azure Service Fabric 应用程序和群集最佳做法

本文提供了有关管理 Azure Service Fabric 应用程序和群集的最佳做法的链接。 我们强烈建议你实施这些做法，以优化生产环境的可靠性。 使用 [Service Fabric 群集模板之一](https://github.com/Azure-Samples/service-fabric-cluster-templates)开始设计生产解决方案，或更新现有模板以纳入这些做法。

## <a name="security"></a>安全性

* [安全性最佳做法](service-fabric-best-practices-security.md)

## <a name="networking"></a>网络

* [网络最佳做法](service-fabric-best-practices-networking.md)

## <a name="compute-planning-and-scaling"></a>计算规划和缩放

* [计算缩放最佳做法](service-fabric-best-practices-capacity-scaling.md)
* [计算容量规划](./service-fabric-cluster-capacity.md)

## <a name="infrastructure-as-code"></a>基础结构即代码

* [用于实现基础结构即代码的最佳做法](service-fabric-best-practices-infrastructure-as-code.md)

## <a name="monitoring-and-diagnostics"></a>监视和诊断

* [群集监视和诊断最佳做法](service-fabric-best-practices-monitoring.md)

## <a name="application-design"></a>应用程序设计

* [应用程序设计的最佳做法](service-fabric-best-practices-applications.md)

## <a name="checklist"></a>清单

实施前面几节中建议的做法后，请确保已将所有最佳做法集成到生产就绪情况核对清单中：
* [Azure Service Fabric 生产准备情况清单](./service-fabric-production-readiness-checklist.md)

## <a name="next-steps"></a>后续步骤

* 在运行 Windows Server 的 VM 或计算机上创建群集：[创建适用于 Windows Server 的 Service Fabric 群集](service-fabric-cluster-creation-for-windows-server.md)
* 在运行 Linux 的 VM 或计算机上创建群集：[创建 Linux 群集](service-fabric-cluster-creation-via-portal.md)
* Service Fabric 故障排除：[故障排除指南](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)
