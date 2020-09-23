---
title: Azure 安全中心与 Azure Kubernetes 服务
description: 了解 Azure 安全中心与 Azure Kubernetes 服务的集成
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: f1444ea31fe693f1c912a3ecb785bbb1e3aa6333
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90977278"
---
# <a name="azure-kubernetes-services-integration-with-security-center"></a>Azure Kubernetes 服务与安全中心的集成

Azure Kubernetes 服务 (AKS) 是 Microsoft 的托管服务，用于开发、部署和管理容器化应用程序。 

启用 Azure **Defender For Kubernetes** 以深入了解你的 AKS 节点、云流量和安全控制。

安全中心和 AKS 共同构成了最优秀的云-原 Kubernetes 安全产品。

## <a name="what-are-the-components-of-security-centers-kubernetes-protection"></a>安全中心的 Kubernetes protection 组件有哪些？

安全中心的 Kubernetes 保护通过两个元素的组合提供：

- **Azure 安全中心的虚拟机威胁防护** -使用安全中心在其他 vm 上使用的同一个 Log Analytics 代理，安全中心可以显示 AKS 节点上发生的安全问题。 代理还监视容器特定的分析。

- **Azure 安全中心的 Kubernetes 计划的可选 Azure Defender** -Kubernetes 计划通过 AKS 服务接收来自 Kubernetes 子系统的日志和信息。 这些日志已通过 AKS 服务在 Azure 中可用。 为 Kubernetes 启用 Azure Defender 时，请授予安全中心对日志的访问权限。 因此安全中心使用已由 AKS 主节点收集的数据为 AKS 群集带来了安全性优势。 Azure 安全中心从 Kubernetes 环境扫描的某些数据可能会包含敏感信息。


## <a name="next-steps"></a>后续步骤

若要详细了解安全中心的容器安全功能，请参阅：

* [Azure 安全中心和容器安全性](container-security.md)

* [与 Azure 容器注册表集成](azure-container-registry-integration.md)

* [Microsoft 的数据管理](https://www.microsoft.com/trust-center/privacy/data-management) -描述 microsoft 服务的数据策略 (包括 Azure、Intune 和 Microsoft 365) 、microsoft 数据管理的详细信息以及影响数据的保留策略
