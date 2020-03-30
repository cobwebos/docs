---
title: 自动修复 Azure 库伯奈斯服务 （AKS） 节点
description: 了解节点自动修复功能，以及 AKS 如何修复损坏的工作节点。
services: container-service
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 9bf9df69a0a6bfa4d9f4029278d2a146811980c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284834"
---
# <a name="azure-kubernetes-service-aks-node-auto-repair"></a>Azure 库伯奈斯服务 （AKS） 节点自动修复

AKS 持续检查辅助节点的运行状况，并在节点不正常时执行自动修复。 本文档介绍 Azure Kubernetes 服务 （AKS） 如何监视辅助节点并修复不正常的工作节点。  本文档旨在通知 AKS 操作员节点修复功能的行为。 还必须注意，Azure 平台对遇到问题的[虚拟机执行维护][vm-updates]。 AKS 和 Azure 协同工作，以最大程度地减少群集的服务中断。

> [!Important]
> Windows Server 节点池当前不支持节点自动修复功能。

## <a name="how-aks-checks-for-unhealthy-nodes"></a>AKS 如何检查不正常的节点

> [!Note]
> AKS 对具有用户帐户**aks-重新中介的**节点执行修复操作。

AKS 使用规则来确定节点是否处于不正常状态，是否需要修复。 AKS 使用以下规则来确定是否需要自动修复。

* 节点在 10 分钟内连续检查时报告 **"不就绪"** 状态
* 节点在 10 分钟内不报告状态

您可以使用 kubectl 手动检查节点的运行状况状态。 

```
kubectl get nodes
```

## <a name="how-automatic-repair-works"></a>自动修复的工作原理

> [!Note]
> AKS 对具有用户帐户**aks-重新中介的**节点执行修复操作。

此行为适用于**虚拟机缩放集**。  自动修复需要几个步骤来修复损坏的节点。  如果确定节点不正常，AKS 将尝试多个修正步骤。  这些步骤按以下顺序执行：

1. 容器运行时在 10 分钟内无响应后，故障运行时服务将重新启动节点上。
2. 如果节点未在 10 分钟内准备就绪，则重新启动该节点。
3. 如果节点未在 30 分钟内准备就绪，则重新映像节点。

> [!Note]
> 如果多个节点不正常，则逐个修复它们

## <a name="next-steps"></a>后续步骤

使用[可用性区域][availability-zones]提高 AKS 群集工作负载的高可用性。

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[availability-zones]: ./availability-zones.md
[vm-updates]: ../virtual-machines/maintenance-and-updates.md
