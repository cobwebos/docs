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
ms.date: 06/30/2020
ms.author: memildin
ms.openlocfilehash: 94d1bccc9a7f45d24d8c5b92aecba54d9f7f630a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85800165"
---
# <a name="azure-kubernetes-services-integration-with-security-center"></a>Azure Kubernetes 服务与安全中心的集成

Azure Kubernetes 服务 (AKS) 是 Microsoft 的托管服务，用于开发、部署和管理容器化应用程序。 

如果你在 Azure 安全中心的标准层上，你可以添加 AKS 捆绑（请参阅[定价](security-center-pricing.md)）以深入了解你的 AKS 节点、云流量和安全控制。

安全中心和 AKS 共同构成了最优秀的云-原 Kubernetes 安全产品。

## <a name="what-are-the-components-of-security-centers-kubernetes-protection"></a>安全中心的 Kubernetes protection 组件有哪些？

安全中心的 Kubernetes 保护通过两个元素的组合提供：

- **Azure 安全中心的虚拟机威胁防护**-使用安全中心在其他 vm 上使用的同一个 Log Analytics 代理，安全中心可以显示 AKS 节点上发生的安全问题。 代理还监视容器特定的分析。

- **Azure 安全中心的可选 Kubernetes 捆绑包**-Kubernetes 捆绑通过 AKS 服务从 Kubernetes 子系统接收日志和信息。 这些日志已通过 AKS 服务在 Azure 中可用。 启用安全中心的 Kubernetes 捆绑时，会授予安全中心对日志的访问权限。 因此安全中心使用已由 AKS 主节点收集的数据为 AKS 群集带来了安全性优势。 Azure 安全中心从 Kubernetes 环境扫描的某些数据可能会包含敏感信息。

    ![Azure 安全中心与 Azure Kubernetes 服务 (AKS) 综合概述](./media/azure-kubernetes-service-integration/aks-asc-integration-overview.png)

## <a name="what-protections-are-provided"></a>提供了哪些保护？

同时使用这两个服务可以提供：

* **安全建议** - 安全中心标识 AKS 资源并对其进行分类：从群集到单个虚拟机。 然后，可以查看每个资源的安全建议。 有关详细信息，请参阅[建议的参考列表](recommendations-reference.md#recs-containers)中的容器建议。 

* **环境强化** - 安全中心持续监视 Kubernetes 群集的配置和 Docker 配置。 然后，它将生成反映行业标准的安全建议。

* **运行时保护**-通过连续分析以下 AKS 源，安全中心会提醒你在主机*和*AKS 群集级别检测到威胁和恶意活动。 [详细了解容器的威胁保护](threat-protection.md#azure-containers)。


     

![详细了解 Azure 安全中心与 Azure Kubernetes 服务 (AKS)](./media/azure-kubernetes-service-integration/aks-asc-integration-detailed.png)



## <a name="aks-with-security-center-faq"></a>AKS 与安全中心常见问题

### <a name="can-i-still-get-aks-protections-without-the-log-analytics-agent"></a>如果没有 Log Analytics 代理，是否仍可获取 AKS 保护？

如上所述，可选的 Kubernetes 捆绑在群集级别提供保护，Azure 安全中心标准层的 Log Analytics 代理保护节点。 

建议同时部署这两个，以实现最完整的保护。

如果选择不在主机上安装代理，则只会收到威胁防护权益和安全警报的子集。 你仍会收到与网络分析以及与恶意服务器通信相关的警报。



## <a name="next-steps"></a>后续步骤

若要详细了解安全中心的容器安全功能，请参阅：

* [Azure 安全中心和容器安全性](container-security.md)

* [与 Azure 容器注册表集成](azure-container-registry-integration.md)

* [Microsoft 的数据管理](https://www.microsoft.com/trust-center/privacy/data-management)-描述 microsoft 服务（包括 Azure、Intune 和 Microsoft 365）的数据策略、microsoft 数据管理的详细信息以及影响数据的保留策略
