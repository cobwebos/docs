---
title: Azure 安全中心和 Azure 库伯奈斯服务
description: 了解 Azure 安全中心与 Azure 库伯奈斯服务的集成
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
ms.openlocfilehash: d1cd4691586b27282d221a19c5fb7a1af034ed6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80125163"
---
# <a name="azure-kubernetes-services-integration-with-security-center"></a>Azure 库伯奈斯服务集成安全中心

Azure 库伯奈斯服务 （AKS） 是 Microsoft 用于开发、部署和管理容器化应用程序的托管服务。 

将 AKS 与 Azure 安全中心的标准层（请参阅[定价](security-center-pricing.md)）结合使用，以更深入地了解 AKS 节点、云流量和安全控制。

使用 AKS 主节点已收集的数据，安全中心为您的 AKS 群集带来安全优势。 

![Azure 安全中心和 Azure 库伯奈斯服务 （AKS） 高级概述](./media/azure-kubernetes-service-integration/aks-asc-integration-overview.png)

这两个工具共同构成了最佳的云原生 Kubernetes 安全产品。 

## <a name="benefits-of-integration"></a>集成的好处

将两种服务结合使用可提供：

* **安全建议**- 安全中心标识您的 AKS 资源并对其进行分类：从群集到单个虚拟机。 然后，您可以查看每个资源的安全建议。 有关详细信息，请参阅[建议参考列表中的容器建议](recommendations-reference.md#recs-computeapp)。 

* **环境强化**- 安全中心不断监控 Kubernetes 群集和 Docker 配置的配置。 然后，它生成反映行业标准的安全建议。

* **运行时保护**- 通过持续分析以下 AKS 源，安全中心会提醒您主机和 AKS 群集级别检测到的威胁*和*恶意活动：
    * 原始安全事件，如网络数据和进程创建
    * 库伯内斯审计日志

    有关详细信息，请参阅[Azure 容器的威胁保护](threat-protection.md#azure-containers)

    有关可能警报的列表，请参阅警报参考表中的这些部分[：AKS 群集级别警报](alerts-reference.md#alerts-akscluster)和[容器主机级别警报](alerts-reference.md#alerts-containerhost)。  

![Azure 安全中心和 Azure 库伯奈斯服务 （AKS） 详解](./media/azure-kubernetes-service-integration/aks-asc-integration-detailed.png)

> [!NOTE]
> Azure 安全中心从库伯奈斯环境扫描的某些数据可能包含敏感信息。


## <a name="next-steps"></a>后续步骤

要了解有关安全中心的容器安全功能的更多，请参阅：

* [Azure 安全中心和容器安全性](container-security.md)

* [与 Azure 容器注册表集成](azure-container-registry-integration.md)

* [Microsoft 的数据管理](https://www.microsoft.com/trust-center/privacy/data-management)- 描述 Microsoft 服务（包括 Azure、Intune 和 Office 365）的数据策略、Microsoft 数据管理的详细信息以及影响数据的保留策略