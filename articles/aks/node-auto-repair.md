---
title: 自动修复 Azure Kubernetes 服务 (AKS) 节点
description: 了解节点自动修复功能，以及 AKS 如何修复损坏的工作器节点。
services: container-service
ms.topic: conceptual
ms.date: 08/24/2020
ms.openlocfilehash: 781a1ffebb40b0cce9f18699d308db90633e8626
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89490099"
---
# <a name="azure-kubernetes-service-aks-node-auto-repair"></a>Azure Kubernetes 服务 (AKS) 节点自动修复

AKS 会持续检查工作器节点的运行状况，在节点运行不正常的情况下对其进行自动修复。 本文档向操作员介绍了自动节点修复功能在 Windows 和 Linux 节点上的行为方式。 除了 AKS 修复，Azure VM 平台还会对遇到问题的[虚拟机执行维护][vm-updates]。 AKS 和 Azure VM 协同工作，以最大程度地减少群集的服务中断次数。

## <a name="how-aks-checks-for-unhealthy-nodes"></a>AKS 如何检查运行不正常的节点

AKS 使用规则来确定节点是否运行不正常以及是否需要修复。 AKS 使用以下规则来确定是否需要自动修复。

* 节点在 10 分钟的时间范围内持续检查时报告状态为“未就绪”
* 节点在 10 分钟内未报告状态

可使用 kubectl 手动检查节点的运行状况状态。

```
kubectl get nodes
```

## <a name="how-automatic-repair-works"></a>自动修复的工作原理

> [!Note]
> AKS 使用用户帐户“aks-remediator”启动修复操作。

如果某个节点运行不正常（根据上述规则确定），并且连续 10 分钟仍不正常，则会执行以下操作。

1. 重新启动该节点
1. 如果重新启动失败，则对该节点重置映像
1. 如果重置映像失败，则创建新节点并对新节点重置映像

如果上述操作均失败，AKS 工程师将详细研究其他修正方法。 如果在运行状况检查期间多个节点运行不正常，则在开始另一个修复之前，将单独修复每个节点。

## <a name="next-steps"></a>后续步骤

使用[可用性区域][availability-zones]增加 AKS 群集工作负载的高可用性。

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[availability-zones]: ./availability-zones.md
[vm-updates]: ../virtual-machines/maintenance-and-updates.md
