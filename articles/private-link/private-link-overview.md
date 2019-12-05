---
title: 什么是 Azure 专用链接？
description: 了解如何使用 Azure 专用链接通过虚拟网络中的专用终结点访问 Azure PaaS 服务（例如，Azure 存储和 SQL 数据库）和 Azure 托管的客户服务/合作伙伴服务。
services: private-link
author: asudbring
ms.service: private-link
ms.topic: overview
ms.date: 11/04/2019
ms.author: allensu
ms.openlocfilehash: 03fdd67adf5df440cafe4e7411064437b8ef89ac
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671542"
---
# <a name="what-is-azure-private-link-preview"></a>什么是 Azure 专用链接？ （预览版）
使用 Azure 专用链接，可以通过虚拟网络中的[专用终结点](private-endpoint-overview.md)访问 Azure PaaS 服务（例如，Azure 存储、Azure Cosmos DB 和 SQL 数据库）和 Azure 托管的客户服务/合作伙伴服务。 虚拟网络与服务之间的流量将通过 Microsoft 主干网络，因此不会从公共 Internet 泄露。 你还可以在虚拟网络 (VNet) 中创建自己的[专用链接服务](private-link-service-overview.md)，并将其专门提供给自己的客户。 使用 Azure 专用链接的设置和使用体验在 Azure PaaS、客户自有服务和共享合作伙伴服务中是一致的。

> [!IMPORTANT]
> 此公共预览版在提供时没有附带服务级别协议，不应用于生产工作负荷。 某些功能可能不受支持或受到约束，或者不一定在所有 Azure 位置都可用。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 有关已知的限制，请参阅[专用终结点](private-endpoint-overview.md#limitations)和[专用链接服务](private-link-service-overview.md#limitations)。


![专用终结点概述](media/private-link-overview/private-endpoint.png)

## <a name="key-benefits"></a>主要优点
Azure 专用链接提供以下优势：  
- **以私密方式访问 Azure 平台上的服务**：以私密方式将虚拟网络连接到 Azure 中运行的服务，而无需在源或目标上使用公共 IP 地址。 服务提供商可在自己的虚拟网络中以私密方式呈现其服务，而使用者可在其本地虚拟网络中以私密方式访问这些服务。 专用链接平台将通过 Azure 主干网络处理使用者与服务之间的连接。 
 
- **本地网络和对等互连的网络**：使用专用终结点通过 ExpressRoute 专用对等互连/VPN 隧道和对等互连的虚拟网络从本地访问 Azure 中运行的服务。 无需设置公共对等互连或遍历 Internet 即可访问服务。 此功能可让客户安全地将工作负荷迁移到 Azure。
 
- **防范数据渗透**：VNet 中的专用终结点通过 Azure 专用链接映射到客户 PaaS 资源的特定实例，而不是映射到整个服务。 使用专用终结点，使用者可以做到仅连接到特定的资源，而不连接到服务中的任何其他资源。 这种内置机制可以防范数据渗透的风险。 
 
- **全球覆盖**：以私密方式连接到在其他区域中运行的服务。 这意味着，使用者的虚拟网络可以位于区域 A，而且可以连接到区域 B 中专用链接后面的服务。  
 
- **扩展到自己的服务**：利用相同的体验和功能以私密方式将自己的服务呈现给 Azure 中的使用者。 将服务放在标准负载均衡器的后面即可为其启用专用链接。 然后，使用者可以使用其自己的 VNet 中的专用终结点直接连接到你的服务。 可以使用简单的审批调用流来管理这些连接请求。 Azure 专用链接也适用于属于不同 Active Directory 租户的使用者和服务。 

## <a name="availability"></a>可用性 
 下表列出了专用链接服务及其适用的区域。 

|场景  |支持的服务  |可用区域 | 状态  |
|:---------|:-------------------|:-----------------|:--------|
|客户自有服务的专用链接|标准负载均衡器后面的专用链接服务 | 所有公共区域  | 预览  |
|Azure PaaS 服务的专用链接   | Azure 存储        |  所有公共区域      | 预览 <br/> [了解详细信息](/azure/storage/common/storage-private-endpoints)。  |
|  | Azure Data Lake Storage Gen2        |  所有公共区域      | 预览 <br/> [了解详细信息](/azure/storage/common/storage-private-endpoints)。  |
|  |  Azure SQL 数据库         | 所有公共区域      |   预览      |
||Azure SQL 数据仓库| 所有公共区域 |预览|
||Azure Cosmos DB| 美国中西部、美国西部、美国中北部 |预览|

有关最新通知，请查看 [Azure 虚拟网络更新页](https://azure.microsoft.com/updates/?product=virtual-network)。

## <a name="logging-and-monitoring"></a>日志记录和监视

Azure 专用链接与 Azure Monitor 集成，使你能够在存储帐户中存档日志、将事件流式传输到事件中心，或者将其发送到 Azure Monitor 日志。 可以在 Azure Monitor 中访问以下信息： 
- **专用终结点**：专用终结点处理的数据（传入/传出）
 
- **专用链接服务**：
    - 专用链接服务处理的数据（传入/传出）
    - NAT 端口可用性  
 
## <a name="pricing"></a>定价   
有关定价详细信息，请参阅 [Azure 专用链接定价](https://azure.microsoft.com/pricing/details/private-link/)。
 
## <a name="faqs"></a>常见问题解答  
有关常见问题解答，请参阅 [Azure 专用链接常见问题解答](private-link-faq.md)。
 
## <a name="limits"></a>限制  
有关限制，请参阅 [Azure 专用链接的限制](../azure-subscription-service-limits.md#private-link-limits)。

## <a name="next-steps"></a>后续步骤
- [使用门户创建 SQL 数据库服务器的专用终结点](create-private-endpoint-portal.md)
- [使用 PowerShell 创建 SQL 数据库服务器的专用终结点](create-private-endpoint-powershell.md)
- [使用 CLI 创建 SQL 数据库服务器的专用终结点](create-private-endpoint-cli.md)
- [使用门户创建存储帐户的专用终结点](create-private-endpoint-storage-portal.md)
- [使用门户创建 Azure Cosmos 帐户的专用终结点](../cosmos-db/how-to-configure-private-endpoints.md)
- [使用 Azure PowerShell 创建自己的专用链接服务](create-private-link-service-powershell.md)


 
