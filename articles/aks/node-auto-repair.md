---
title: 自动修复 Azure Kubernetes 服务（AKS）节点
description: 了解节点自动修复功能，以及 AKS 如何修复损坏的工作节点。
services: container-service
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 9bf9df69a0a6bfa4d9f4029278d2a146811980c8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80284834"
---
# <a name="azure-kubernetes-service-aks-node-auto-repair"></a>Azure Kubernetes 服务（AKS）节点自动修复

AKS 连续检查工作节点的运行状况状态，并在节点变得不正常时执行节点的自动修复。 本文档介绍 Azure Kubernetes 服务（AKS）如何监视工作节点，以及修复不正常的工作节点。  该文档用于通知 AKS 操作员对节点修复功能的行为。 另外，请务必注意，Azure 平台会在遇到问题的[虚拟机上执行维护][vm-updates]。 AKS 和 Azure 一起工作，最大程度减少群集的服务中断。

> [!Important]
> Windows Server 节点池目前不支持节点自动修复功能。

## <a name="how-aks-checks-for-unhealthy-nodes"></a>AKS 如何检查不正常节点

> [!Note]
> AKS 对具有用户帐户**AKS-remediator**的节点执行修复操作。

AKS 使用规则来确定节点是否处于不正常状态并需要修复。 AKS 使用以下规则来确定是否需要自动修复。

* 节点在10分钟的时间范围内报告连续检查的**NotReady**状态
* 节点未在10分钟内报告状态

可以通过 kubectl 手动检查节点的运行状况状态。 

```
kubectl get nodes
```

## <a name="how-automatic-repair-works"></a>自动修复的工作方式

> [!Note]
> AKS 对具有用户帐户**AKS-remediator**的节点执行修复操作。

此行为适用于**虚拟机规模集**。  自动修复执行几个步骤来修复已损坏的节点。  如果将某个节点确定为不正常，AKS 将尝试多次修正步骤。  按照以下顺序执行这些步骤：

1. 容器运行时在10分钟内无响应后，会在节点上重启失败的运行时服务。
2. 如果节点未在10分钟内就绪，则重新启动节点。
3. 如果节点在30分钟内未准备就绪，则会重新映像该节点。

> [!Note]
> 如果多个节点不正常，则会逐个修复这些节点

## <a name="next-steps"></a>后续步骤

使用[可用性区域][availability-zones]提高 AKS 群集工作负荷的高可用性。

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[availability-zones]: ./availability-zones.md
[vm-updates]: ../virtual-machines/maintenance-and-updates.md
