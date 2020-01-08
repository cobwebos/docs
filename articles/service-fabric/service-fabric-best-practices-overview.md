---
title: Azure Service Fabric 应用程序和群集最佳做法
description: 使用 Azure Service Fabric 管理群集、应用和服务的最佳实践和设计注意事项。
author: peterpogorski
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: pepogors
ms.openlocfilehash: 46601fd91fccb5bd0866bd999cc7643c37c6d582
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2019
ms.locfileid: "75551771"
---
# <a name="azure-service-fabric-application-and-cluster-best-practices"></a>Azure Service Fabric 应用程序和群集最佳做法

本文提供了有关管理 Azure Service Fabric 应用程序和群集的最佳实践的链接。 我们强烈建议你实现这些做法来优化生产环境的可靠性。 使用其中一个[Service Fabric 群集模板](https://github.com/Azure-Samples/service-fabric-cluster-templates)开始设计你的生产解决方案，或更新现有模板以合并这些操作。

## <a name="security"></a>安全性

* [安全性最佳做法](service-fabric-best-practices-security.md)

## <a name="networking"></a>联网

* [网络最佳做法](service-fabric-best-practices-networking.md)

## <a name="compute-planning-and-scaling"></a>计算规划和缩放

* [计算缩放最佳做法](service-fabric-best-practices-capacity-scaling.md)
* [计算容量规划](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)

## <a name="infrastructure-as-code"></a>基础结构即代码

* [用于实现基础结构即代码的最佳做法](service-fabric-best-practices-infrastructure-as-code.md)

## <a name="monitoring-and-diagnostics"></a>监视和诊断

* [群集监视和诊断最佳做法](service-fabric-best-practices-monitoring.md)

## <a name="application-design"></a>应用程序设计

* [应用程序设计的最佳做法](service-fabric-best-practices-applications.md)

## <a name="checklist"></a>清单

实现了前面几节中建议的做法后，请确保已在生产准备情况清单中集成了所有最佳做法：
* [Azure Service Fabric 生产准备情况清单](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist)

## <a name="next-steps"></a>后续步骤

* 在运行 Windows Server 的 VM 或计算机上创建群集：[创建适用于 Windows Server 的 Service Fabric 群集](service-fabric-cluster-creation-for-windows-server.md)
* 在运行 Linux 的 VM 或计算机上创建群集：[创建 Linux 群集](service-fabric-cluster-creation-via-portal.md)
* 疑难解答 Service Fabric：[疑难解答指南](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)