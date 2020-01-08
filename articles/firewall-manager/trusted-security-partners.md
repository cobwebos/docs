---
title: 什么是 Azure 防火墙管理器可信安全合作伙伴（预览版）
description: 了解 Azure 防火墙管理器信任的安全合作伙伴
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: victorh
ms.openlocfilehash: b92242ce9086579d0397f78853402cfc08453f68
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75436777"
---
# <a name="what-are-trusted-security-partners-preview"></a>什么是信任的安全合作伙伴（预览版）？

> [!IMPORTANT]
> 此公共预览版在提供时没有附带服务级别协议，不应用于生产工作负荷。 某些功能可能不受支持或受到约束，或者不一定在所有 Azure 位置都可用。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure 防火墙管理器中的*受信任安全合作伙伴（预览版）* 可让你使用熟悉的、一流的第三方安全即服务（SECaaS）产品来保护用户的 Internet 访问。

通过快速配置，你可以使用受支持的安全合作伙伴保护集线器，并从虚拟网络（Vnet）或区域中的分支机构路由和筛选 Internet 流量。 这是使用自动路由管理实现的，无需设置和管理用户定义的路由（Udr）。

你可以在多个 Azure 区域中部署配置有所选安全合作伙伴的安全中心，以便在这些区域中的任何位置获得用户的连接和安全。 由于能够使用安全合作伙伴提供的 Internet/SaaS 应用程序流量和 Azure Firewall，在受保护的中心获取专用流量，因此，你现在可以在 Azure 上开始构建接近全球分布式用户和应用程序的安全边缘。

对于此预览版，支持的安全合作伙伴是**ZScaler**和**iboss**。 支持的区域包括： WestCentralUS、NorthCentralUS、WestUS、WestUS2 和 EastUS。

![信任的安全合作伙伴](media/trusted-security-partners/trusted-security-partners.png)

## <a name="key-scenarios"></a>关键方案

在以下情况下，可以使用安全合作伙伴筛选 Internet 流量：

- 虚拟网络（VNet）到 Internet

   为在 Azure 上运行的云工作负荷提供高级用户感知型 Internet 保护。

- 分支到 Internet

   利用 Azure 连接和全局分发功能，可以轻松地为分支到 Internet 方案添加第三方 NSaaS 筛选。 可以使用 Azure 虚拟广域网构建全球传输网络和安全边缘。

支持以下方案：
-   通过第三方合作伙伴产品/服务将 VNet 连接到 Internet。
-   通过第三方合作伙伴产品分支到 Internet。
-   通过 Azure 防火墙通过第三方合作伙伴产品/服务（分支到辐射、分支到分支、分支到轮辐）分支到 Internet。

不支持以下方案：

- 通过合作伙伴产品/服务的 VNet 到 Internet 不能与用于专用流量的 Azure 防火墙结合使用。 请参阅以下限制。

## <a name="current-limitations"></a>当前限制

- 对于 VNet 到 Internet，你无法将添加用于专用流量的 Azure 防火墙和用于 Internet 流量的合作伙伴产品组合。 你可以将 Internet 流量发送到受保护虚拟中心中的 Azure 防火墙或第三方安全合作伙伴产品，但不能同时发送这两者。 
- 每个虚拟中心最多可以部署一个安全伙伴。 如果需要更改提供程序，则必须删除现有伙伴并添加一个新伙伴。

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

此外，出于性能方面的考虑，Office 365 连接出于隐私目的进行了强加密，并使用有效的专用协议。 这会使它不切实际，并有影响力这些与传统网络级别安全解决方案的连接。 出于此原因，我们强烈建议客户在通过 Azure 发送其他流量之前，直接从分支发送 Office 365 流量。 Microsoft 已与多个与 Azure 和 Office 365 集成的 SD WAN 解决方案提供商合作，使客户能够轻松实现 Office 365 的直接和本地 Internet 分类。 有关详细信息，请参阅[通过虚拟 WAN 设置我的 O365 策略如何实现？](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-office365-overview)


## <a name="next-steps"></a>后续步骤

[使用 Azure 防火墙管理器在受保护的中心部署受信任的安全产品](deploy-trusted-security-partner.md)。
