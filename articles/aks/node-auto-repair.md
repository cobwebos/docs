---
title: 自动修复 Azure Kubernetes 服务 (AKS) 节点
description: 了解节点自动修复功能，以及 AKS 如何修复损坏的工作器节点。
services: container-service
ms.topic: conceptual
ms.date: 06/02/2020
ms.openlocfilehash: 91384461567634faabaaa1dd588d6e7ec6ece60e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84735619"
---
# <a name="azure-kubernetes-service-aks-node-auto-repair"></a>Azure Kubernetes 服务 (AKS) 节点自动修复

AKS 会持续检查工作器节点的运行状况，在节点运行不正常的情况下对其进行自动修复。 本文档通知操作员有关自动节点修复功能的行为方式。 除了 AKS 修复以外，Azure VM 平台还会在遇到问题的[虚拟机上执行维护][vm-updates]。 AKS 和 Azure Vm 共同合作，最大程度减少群集的服务中断。

> [!Important]
> Windows Server 节点池目前不支持节点自动修复功能。

## <a name="how-aks-checks-for-unhealthy-nodes"></a>AKS 如何检查运行不正常的节点

> [!Note]
> AKS 使用用户帐户 aks-remediator  对节点执行修复操作。

AKS 使用规则来确定节点是否不正常并需要修复。 AKS 使用以下规则来确定是否需要自动修复。

* 节点在 10 分钟的时间范围内持续检查时报告状态为“未就绪” 
* 节点在 10 分钟内未报告状态

可使用 kubectl 手动检查节点的运行状况状态。 

```
kubectl get nodes
```

## <a name="how-automatic-repair-works"></a>自动修复的工作原理

> [!Note]
> AKS 启动具有用户帐户**AKS-remediator**的修复操作。

默认情况下，VM 集类型为**虚拟机规模集**的群集支持自动修复。 如果根据以上规则确定节点不正常，AKS 将在10个连续的不正常分钟后重新启动该节点。 如果节点在初始修复操作后仍保持不正常，则 AKS 工程师将调查其他修正。
  
如果在运行状况检查过程中多个节点不正常，则每个节点在另一个修复开始之前单独修复。

## <a name="next-steps"></a>后续步骤

使用[可用性区域][availability-zones]增加 AKS 群集工作负载的高可用性。

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[availability-zones]: ./availability-zones.md
[vm-updates]: ../virtual-machines/maintenance-and-updates.md
