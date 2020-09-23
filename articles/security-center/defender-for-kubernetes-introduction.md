---
title: Azure Defender for Kubernetes-优势和功能
description: 了解适用于 Kubernetes 的 Azure Defender 的优势和功能。
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: conceptual
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: e72875135243733a6acf03bb3aa6fb9405392d9d
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90934745"
---
# <a name="introduction-to-azure-defender-for-kubernetes"></a>Azure Defender for Kubernetes 简介

Azure Kubernetes 服务 (AKS) 是 Microsoft 的托管服务，用于开发、部署和管理容器化应用程序。

Azure 安全中心和 AKS 构成了最佳的云本地 Kubernetes 安全产品和服务，它们提供了环境强化、工作负荷保护和运行时保护，如下所述。

对于 Kubernetes 群集的威胁检测，请启用 **Azure Defender For Kubernetes**。

如果 [为服务器启用了 Azure Defender](defender-for-servers-introduction.md)，则可以使用适用于 Linux AKS 节点的主机级威胁检测。

## <a name="availability"></a>可用性

|方面|详细信息|
|----|:----|
|发布状态：|正式发布 (GA)|
|计价|**Azure Defender For Kubernetes**按[定价页](security-center-pricing.md)中所示计费|
|必需的角色和权限：|**安全管理员** 可以解除警报。<br>**安全读取者**可以查看结果。|
|云：|![是](./media/icons/yes-icon.png) 商业云<br>![否](./media/icons/no-icon.png) 国家/主权 (US Gov、中国 Gov、其他 Gov) |
|||

## <a name="what-are-the-benefits-of-azure-defender-for-kubernetes"></a>Azure Defender for Kubernetes 有哪些好处？

### <a name="run-time-protection"></a>运行时保护

通过连续分析以下 AKS 源，安全中心为容器化环境提供实时威胁防护，并为在主机 *和* AKS 群集级别检测到的威胁和恶意活动生成警报。 可以使用此信息快速补救安全问题，并提高容器的安全性。

安全中心在不同的级别提供威胁防护： 

- **Azure defender 为服务器提供的主机级别 () ** -使用安全中心在其他 vm 上使用的同一个 Log Analytics 代理，Azure Defender 会监视你的 Linux AKS 节点中是否存在可疑活动，如 web shell 检测和与已知的可疑 IP 地址的连接。 此代理还监视容器特定的分析，如特权容器的创建、对 API 服务器的可疑访问以及安全外壳在 Docker 容器中运行 (SSH) 服务器。

    有关 AKS 主机级别警报的列表，请参阅警报的 [引用表](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-containerhost)。

    >[!IMPORTANT]
    > 如果你选择不在主机上安装代理，则只能收到一部分威胁防护权益和安全警报。 你仍会收到与网络分析以及与恶意服务器通信相关的警报。

- **Azure Defender 为 Kubernetes) 提供 AKS 群集级别 (** -在群集级别，威胁防护基于分析 Kubernetes 审核日志。 若要启用此 **无代理** 监视，请启用 Azure Defender。 为了在此级别生成警报，安全中心将使用 AKS 检索到的日志来监视 AKS 管理的服务。 此级别的事件示例包括公开 Kubernetes 仪表板、创建高特权角色，以及创建敏感的装入点。

    有关 AKS 群集级别的警报列表，请参阅[警报参考表](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-akscluster)。

    >[!NOTE]
    > 安全中心针对在订阅设置中启用“Kubernetes”选项后发生的 Azure Kubernetes 服务操作和部署生成安全警报。 

此外，我们的全球安全研究团队会不断监视威胁态势。 一旦发现威胁，他们就会添加容器特定的警报和漏洞。

> [!TIP]
> 可以按照[此博客文章](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270)中的说明来模拟容器警报。



## <a name="how-does-security-centers-kubernetes-protection-work"></a>安全中心的 Kubernetes 保护的工作原理是什么？

下面是有关 Azure 安全中心、Azure Kubernetes 服务和 Azure 策略之间的交互的高级关系图。

你可以看到安全中心接收和分析的项包括：

- 来自 API 服务器的审核日志
- Log Analytics 代理中的原始安全事件
- 来自 AKS 群集的群集配置信息
- 通过 Kubernetes) 的 **Azure 策略外接程序** 从 azure 策略 (进行工作负荷配置。 [了解有关使用 Kubernetes 许可控制的工作负荷保护最佳实践的详细信息](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control)

:::image type="content" source="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png" alt-text="Azure 安全中心、Azure Kubernetes 服务和 Azure 策略之间交互的高级体系结构" lightbox="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png":::




## <a name="azure-defender-for-kubernetes---faq"></a>Azure Defender for Kubernetes-常见问题

### <a name="can-i-still-get-aks-protections-without-the-log-analytics-agent"></a>如果没有 Log Analytics 代理，是否仍可获取 AKS 保护？

如上所述，适用于 Kubernetes 的可选 **Azure defender** 计划在群集级别提供保护， **Azure defender for server** 的 Log Analytics 代理可保护你的节点。 

建议同时部署这两个，以实现最完整的保护。

如果选择不在主机上安装代理，则只会收到威胁防护权益和安全警报的子集。 你仍会收到与网络分析以及与恶意服务器通信相关的警报。


## <a name="next-steps"></a>后续步骤

本文介绍了安全中心的 Kubernetes 保护，包括用于 Kubernetes 的 Azure Defender。 

如需相关材料，请参阅以下文章： 

- [启用 Azure Defender](security-center-pricing.md)
- [将警报导出到 Azure Sentinel 或第三方 SIEM](continuous-export.md)
- [警报的引用表](alerts-reference.md)