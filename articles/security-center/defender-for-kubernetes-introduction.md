---
title: 适用于 Kubernetes 的 Azure Defender - 优点和功能
description: 了解适用于 Kubernetes 的 Azure Defender 的优点和功能。
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 3308a72421b851402642f12daf56359c7e3c9216
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91449071"
---
# <a name="introduction-to-azure-defender-for-kubernetes"></a>适用于 Kubernetes 的 Azure Defender 简介

Azure Kubernetes 服务 (AKS) 是 Microsoft 的托管服务，用于开发、部署和管理容器化应用程序。

Azure 安全中心和 AKS 构成了最佳的云原生 Kubernetes 安全产品和服务，它们提供了环境强化功能、工作负载保护和运行时间保护，如下所述。

若要对 Kubernetes 群集进行威胁检测，请启用适用于 Kubernetes 的 Azure Defender。

如果[为服务器启用 Azure Defender](defender-for-servers-introduction.md)，则可以使用适用于 Linux AKS 节点的主机级威胁检测。

## <a name="availability"></a>可用性

|方面|详细信息|
|----|:----|
|发布状态：|正式发布 (GA)|
|定价：|适用于 Kubernetes 的 Azure Defender 按[定价页](security-center-pricing.md)中的定价计费|
|所需角色和权限：|**安全管理员**可以消除警报。<br>**安全读取者**可以查看结果。|
|云：|![是](./media/icons/yes-icon.png) 商业云<br>![否](./media/icons/no-icon.png) 国家/主权（US Gov、中国 Gov、其他 Gov）|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-kubernetes"></a>适用于 Kubernetes 的 Azure Defender 有哪些优点？

### <a name="run-time-protection"></a>运行时间保护

通过连续分析以下 AKS 源，安全中心为容器化环境提供实时威胁防护，并为在主机和 AKS 群集级别检测到的威胁和恶意活动生成警报。 可以使用此信息快速补救安全问题，并提高容器的安全性。

安全中心在不同的级别提供威胁防护： 

- **主机级别（由适用于服务器的 Azure Defender 提供）** - Azure Defender 利用安全中心在其他 VM 上使用的同一 Log Analytics 代理监视你的 Linux AKS 节点中是否存在可疑活动，例如 web shell 检测和与已知的可疑 IP 地址进行连接。 该代理还会监视特定于容器的分析，例如创建特权容器、以可疑方式访问 API 服务器以及在 Docker 容器内部运行安全外壳 (SSH) 服务器。

    有关 AKS 主机级别的警报列表，请参阅[警报参考表](alerts-reference.md#alerts-containerhost)。

    >[!IMPORTANT]
    > 如果你选择不在主机上安装代理，则只能收到一部分威胁防护权益和安全警报。 你仍会收到与网络分析以及与恶意服务器通信相关的警报。

- **AKS 群集级别（由适用于 Kubernetes 的 Azure Defender 提供）** - 在群集级别，威胁防护基于对 Kubernetes 审核日志的分析。 要启用此无代理监视，请启用 Azure Defender。 为了在此级别生成警报，安全中心将使用 AKS 检索到的日志来监视 AKS 管理的服务。 此级别的事件示例包括公开 Kubernetes 仪表板、创建高特权角色，以及创建敏感的装入点。

    有关 AKS 群集级别的警报列表，请参阅[警报参考表](alerts-reference.md#alerts-akscluster)。

    >[!NOTE]
    > 安全中心针对在订阅设置中启用“Kubernetes”选项后发生的 Azure Kubernetes 服务操作和部署生成安全警报。 

此外，我们的全球安全研究团队会不断监视威胁态势。 一旦发现威胁，他们就会添加容器特定的警报和漏洞。

> [!TIP]
> 可以按照[此博客文章](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270)中的说明来模拟容器警报。



## <a name="how-does-security-centers-kubernetes-protection-work"></a>安全中心的 Kubernetes 保护如何工作？

下面是有关 Azure 安全中心、Azure Kubernetes 服务和 Azure Policy 之间的交互的高级关系图。

你可以看到安全中心接收和分析的项包括：

- 来自 API 服务器的审核日志
- Log Analytics 代理中的原始安全事件
- 来自 AKS 群集的群集配置信息
- Azure Policy 中的工作负载配置（通过适用于 Kubernetes 的 Azure Policy 加载项）。 [详细了解如何使用 Kubernetes 准入控制实现工作负载保护的最佳做法](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control)

:::image type="content" source="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png" alt-text="有关 Azure 安全中心、Azure Kubernetes 服务和 Azure Policy 之间的交互的高级关系图" lightbox="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png":::




## <a name="azure-defender-for-kubernetes---faq"></a>适用于 Kubernetes 的 Azure Defender - 常见问题解答

### <a name="can-i-still-get-aks-protections-without-the-log-analytics-agent"></a>如果没有 Log Analytics 代理，是否仍可获得 AKS 保护？

如上所述，适用于 Kubernetes 的 Azure Defender 可选计划在群集级别提供保护，适用于服务器的 Azure Defender 的 Log Analytics 代理会保护节点 。 

建议两者同时部署，以实现最完整的保护。

如果你选择不在主机上安装代理，则只能收到一部分威胁防护权益和安全警报。 你仍会收到与网络分析以及与恶意服务器通信相关的警报。


## <a name="next-steps"></a>后续步骤

本文介绍了安全中心的 Kubernetes 保护，包括适用于 Kubernetes 的 Azure Defender。 

如需相关材料，请参阅以下文章： 

- [启用 Azure Defender](security-center-pricing.md)
- [将警报导出到 Azure Sentinel 或第三方 SIEM](continuous-export.md)
- [警报的引用表](alerts-reference.md)