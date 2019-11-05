---
title: Azure 安全中心和 Azure Kubernetes 服务 |Microsoft Docs
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
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 9bc01e0e703ea9f98d877be39011dcca7c7b284a
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73521756"
---
# <a name="azure-kubernetes-services-integration-with-security-center-preview"></a>Azure Kubernetes 服务与安全中心的集成（预览版）
Azure Kubernetes Service （AKS）是 Microsoft 的托管服务，用于开发、部署和管理容器化应用程序。 

结合使用 AKS 和 Azure 安全中心的标准层（请参阅[定价](security-center-pricing.md)），更深入地了解你的 AKS 节点、云流量和安全控制。

安全中心使用已由 AKS 主节点收集的数据为 AKS 群集带来了安全性优势。 

![Azure 安全中心和 Azure Kubernetes Service （AKS）高级概述](./media/azure-kubernetes-service-integration/aks-asc-integration-overview.png)

这两个工具共同构成了最优秀的云-原 Kubernetes 安全产品。 

## <a name="benefits-of-integration"></a>集成的好处

同时使用这两个服务提供：

* **安全建议**-安全中心标识 AKS 资源并对其进行分类：从群集到单个虚拟机。 然后，可以查看每个资源的安全建议。 有关详细信息，请参阅[如何实施安全建议](security-center-recommendations.md)。 

    > [!NOTE]
    > 如果安全中心建议的名称以 "（预览版）" 标记结尾，则它引用建议的预览性质;不是此功能。

* **环境强化**-安全中心持续监视 Kubernetes 群集的配置，并生成反映行业标准的安全建议。

* **运行时保护**-通过连续分析以下 AKS 源，安全中心会提醒你在主机*和*AKS 群集级别检测到威胁和恶意活动（有关详细信息，请参阅[Azure 容器服务](https://docs.microsoft.com/azure/security-center/security-center-alerts-compute#azure-container-service-)):
    * 原始安全事件，如网络数据和进程创建
    * Kubernetes 审核日志

![更详细地了解 azure 安全中心和 Azure Kubernetes Service （AKS）](./media/azure-kubernetes-service-integration/aks-asc-integration-detailed.png)

> [!NOTE]
> Azure 安全中心从 Kubernetes 环境扫描的某些数据可能包含敏感信息。

## <a name="next-steps"></a>后续步骤

若要了解有关安全中心容器安全功能的详细信息，请参阅：

* [Azure 安全中心和容器安全性](container-security.md)

* [与 Azure 容器注册表的集成](azure-container-registry-integration.md)

* [虚拟机保护](security-center-virtual-machine-protection.md)-介绍安全中心的建议

* [Microsoft 的数据管理](https://www.microsoft.com/trust-center/privacy/data-management)-描述 microsoft 服务（包括 Azure、Intune 和 Office 365）的数据策略、microsoft 数据管理的详细信息以及影响数据的保留策略