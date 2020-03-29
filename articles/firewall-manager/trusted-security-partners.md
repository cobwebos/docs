---
title: 什么是 Azure 防火墙管理器受信任的安全合作伙伴（预览版）
description: 了解 Azure 防火墙管理器受信任的安全合作伙伴
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: victorh
ms.openlocfilehash: b92242ce9086579d0397f78853402cfc08453f68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75436777"
---
# <a name="what-are-trusted-security-partners-preview"></a>什么是信任的安全合作伙伴（预览版）？

> [!IMPORTANT]
> 此公共预览版在提供时没有附带服务级别协议，不应用于生产工作负荷。 某些功能可能不受支持或受到约束，或者不一定在所有 Azure 位置都可用。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure 防火墙管理器中的*可信安全合作伙伴（预览版）* 允许您使用您熟悉的、同类最佳的第三方安全作为服务 （SECaaS） 产品来保护用户的 Internet 访问。

通过快速配置，您可以使用受支持的安全合作伙伴保护集线器，并从虚拟网络 （VNet） 或区域内的分支位置路由和筛选 Internet 流量。 这是使用自动路由管理完成的，无需设置和管理用户定义的路由 （UDR）。

您可以在多个 Azure 区域中部署由您选择的安全合作伙伴配置的安全集线器，以便这些区域中的全球任何地方的用户获得连接和安全。 借助使用安全合作伙伴的 Internet/SaaS 应用程序流量产品，以及安全集线器中专用流量的 Azure 防火墙，现在可以开始在接近全球分布式用户和应用程序的 Azure 上构建安全边缘。

对于此预览，支持的安全合作伙伴是**ZScaler**和**iboss**。 支持的区域是西中环、北中美、西US、西US2和东US。

![信任的安全合作伙伴](media/trusted-security-partners/trusted-security-partners.png)

## <a name="key-scenarios"></a>密钥方案

在以下情况下，您可以使用安全合作伙伴来筛选 Internet 流量：

- 虚拟网络 （VNet） 到互联网

   为在 Azure 上运行的云工作负荷提供高级用户感知型 Internet 保护。

- 互联网分支

   利用 Azure 连接和全局分发，轻松将第三方 NSaaS 筛选添加到 Internet 方案中的分支。 您可以使用 Azure 虚拟 WAN 构建全局传输网络和安全边缘。

支持以下方案：
-   通过第三方合作伙伴产品向互联网提供 VNet。
-   通过第三方合作伙伴产品向互联网分支机构。
-   通过第三方合作伙伴产品分支到 Internet，其余通过 Azure 防火墙进行专用流量（从分支到分支、分支到分支、分支到分支）。

不支持以下方案：

- 通过合作伙伴产品向 Internet 提供的 VNet 不能与用于专用流量的 Azure 防火墙组合使用。 请参阅以下限制。

## <a name="current-limitations"></a>当前限制

- 对于 VNet 到 Internet，不能混合添加用于专用流量的 Azure 防火墙和用于 Internet 流量的合作伙伴产品。 您可以将 Internet 流量发送到 Azure 防火墙或安全虚拟中心中的第三方安全合作伙伴产品，但不能同时发送这两者。 
- 每个虚拟中心最多只能部署一个安全伙伴。 如果需要更改提供程序，则必须删除现有合作伙伴并添加新合作伙伴。

## <a name="best-practices-for-internet-traffic-filtering-in-secured-virtual-hubs"></a>安全虚拟集线器中互联网流量过滤的最佳做法

互联网流量通常包括 Web 流量。 但它也包括发送到 SaaS 应用程序（如 Office 365 （O365） 和 Azure 公共 PaaS 服务（如 Azure 存储、Azure Sql 等）的流量。 以下是处理这些服务流量的最佳做法建议：

### <a name="handling-azure-paas-traffic"></a>处理 Azure PaaS 流量
 
- 如果流量主要由 Azure PaaS 组成，并且可以使用 IP 地址、FQDN、服务标记或 FQDN 标记筛选应用程序的资源访问，请使用 Azure 防火墙进行保护。

- 如果您的流量由 SaaS 应用程序访问组成，或者您需要用户感知筛选（例如，对于虚拟桌面基础结构 （VDI） 工作负载），或者您需要高级 Internet 筛选功能，请使用集线器中的第三方合作伙伴解决方案。

![Azure 防火墙管理器的所有方案](media/trusted-security-partners/all-scenarios.png)

## <a name="handling-office-365-o365-traffic"></a>处理 Office 365 （O365） 流量

在全球分布的分支位置方案中，在发送 Azure 安全集线器的其余 Internet 流量之前，应直接重定向分支的 Office 365 流量。

对于 Office 365，网络延迟和性能对于成功的用户体验至关重要。 为了围绕最佳性能和用户体验实现这些目标，客户在考虑通过 Azure 路由其余 Internet 流量之前，必须实现 Office 365 直接和本地转义。

[Office 365 网络连接原则](https://docs.microsoft.com/office365/enterprise/office-365-network-connectivity-principles)要求从用户分支或移动设备本地路由关键 Office 365 网络连接，然后直接通过 Internet 路由到最近的 Microsoft 网络存在点。

此外，Office 365 连接出于隐私而进行强加密，出于性能原因使用高效的专有协议。 这使得将这些连接置于传统的网络安全解决方案上是不切实际的和有影响的。 出于这些原因，我们强烈建议客户直接从分支机构发送 Office 365 流量，然后再通过 Azure 发送其余流量。 Microsoft 与多个 SD-WAN 解决方案提供商合作，这些提供商与 Azure 和 Office 365 集成，使客户能够轻松启用 Office 365 直接和本地 Internet 突破。 有关详细信息，请参阅[如何通过虚拟 WAN 设置我的 O365 策略？](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-office365-overview)


## <a name="next-steps"></a>后续步骤

[使用 Azure 防火墙管理器 在安全中心部署受信任的安全产品](deploy-trusted-security-partner.md)。
