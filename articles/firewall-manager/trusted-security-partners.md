---
title: 什么是 Azure 防火墙管理器安全合作伙伴提供程序？
description: 了解 Azure 防火墙管理器安全合作伙伴提供程序
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 34da82510f96ef7bde65ceec397b048c941e3234
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85563607"
---
# <a name="what-are-security-partner-providers"></a>什么是安全合作伙伴提供程序？

使用 Azure 防火墙管理器中的*安全合作伙伴提供程序*，可以使用熟悉的、同类最佳的第三方安全即服务（SECaaS）产品来保护用户对 Internet 的访问。

通过快速配置，你可以使用受支持的安全合作伙伴保护集线器，并从虚拟网络（Vnet）或区域中的分支机构路由和筛选 Internet 流量。 可以通过自动路由管理来实现此目的，无需设置和管理用户定义的路由（Udr）。

你可以在多个 Azure 区域中部署配置有所选安全合作伙伴的安全中心，以便在这些区域中的任何位置获得用户的连接和安全。 由于能够使用安全合作伙伴提供的 Internet/SaaS 应用程序流量和 Azure Firewall，在受保护的中心获取专用流量，因此，你现在可以在 Azure 上开始构建接近全球分布式用户和应用程序的安全边缘。

受支持的安全合作伙伴是**ZScaler**、 **Check Point** （预览版）和**iboss** （预览版）。

![安全合作伙伴提供程序](media/trusted-security-partners/trusted-security-partners.png)

## <a name="key-scenarios"></a>密钥方案

在以下情况下，可以使用安全合作伙伴筛选 Internet 流量：

- 虚拟网络（VNet）到 Internet

   为在 Azure 上运行的云工作负荷提供高级用户感知型 Internet 保护。

- 分支到 Internet

   利用 Azure 连接和全局分发功能，可以轻松地为分支到 Internet 方案添加第三方 NSaaS 筛选。 可以使用 Azure 虚拟广域网构建全球传输网络和安全边缘。

支持以下方案：
- 通过 Azure 防火墙通过安全合作伙伴提供商和其他流量（从辐射、分支到分支、分支到辐射）将 VNet/分支到 Internet。
- 通过安全合作伙伴提供商对到 Internet 的 VNet/分支

## <a name="best-practices-for-internet-traffic-filtering-in-secured-virtual-hubs"></a>安全虚拟中心 Internet 流量筛选的最佳实践

Internet 流量通常包含 web 流量。 但它还包括目标为 SaaS 应用程序（如 Office 365 （O365））和 Azure 公共 PaaS 服务（如 Azure 存储、Azure Sql 等等）的流量。 下面是处理这些服务的流量的最佳实践建议：

### <a name="handling-azure-paas-traffic"></a>处理 Azure PaaS 流量
 
- 如果流量主要包含 Azure PaaS，使用 Azure 防火墙保护，可以使用 IP 地址、Fqdn、服务标记或 FQDN 标记筛选应用程序的资源访问权限。

- 如果流量包含 SaaS 应用程序访问权限，或者需要用户感知筛选（例如，对于虚拟桌面基础结构（VDI）工作负荷）或需要高级 Internet 筛选功能，请在中心使用第三方合作伙伴解决方案。

![Azure 防火墙管理器的所有方案](media/trusted-security-partners/all-scenarios.png)

## <a name="handling-office-365-o365-traffic"></a>处理 Office 365 （O365）流量

在全球分布的分支机构方案中，应在将其余 Internet 流量发送到 Azure 安全中心之前，直接在分支上重定向 Office 365 流量。

对于 Office 365，网络延迟和性能对于成功的用户体验至关重要。 若要实现这些目标以获得最佳的性能和用户体验，客户必须在考虑通过 Azure 路由其他 Internet 流量之前，实现 Office 365 直通和本地转义。

[Office 365 网络连接原则](https://docs.microsoft.com/office365/enterprise/office-365-network-connectivity-principles)调用，将从用户分支或移动设备本地路由的关键 Office 365 网络连接，并直接通过 Internet 连接到最接近的 Microsoft 网络状态点。

此外，出于性能方面的考虑，Office 365 连接出于隐私目的进行加密，并使用有效的专用协议。 这会使它不切实际，并有影响力这些与传统网络级别安全解决方案的连接。 出于此原因，我们强烈建议客户在通过 Azure 发送其他流量之前，直接从分支发送 Office 365 流量。 Microsoft 已与多个与 Azure 和 Office 365 集成的 SD WAN 解决方案提供商合作，使客户能够轻松实现 Office 365 的直接和本地 Internet 分类。 有关详细信息，请参阅[通过虚拟 WAN 设置我的 O365 策略如何实现？](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-office365-overview)

## <a name="next-steps"></a>后续步骤

[使用 Azure 防火墙管理器在受保护的中心部署安全合作伙伴产品](deploy-trusted-security-partner.md)。
