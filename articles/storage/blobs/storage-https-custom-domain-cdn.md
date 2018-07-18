---
title: 使用 Azure CDN 通过 HTTPS 访问包含自定义域的 Blob
description: 了解如何将 Azure CDN 与 Blob 存储进行集成以通过 HTTPS 访问包含自定义域的 Blob
services: storage
documentationcenter: ''
author: michaelhauss
manager: vamshik
editor: tysonn
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2018
ms.author: mihauss
ms.openlocfilehash: b3b1b5064e51b68bb64cb8c4dbec6075705795d6
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37025128"
---
# <a name="using-the-azure-cdn-to-access-blobs-with-custom-domains-over-https"></a>使用 Azure CDN 通过 HTTPS 访问包含自定义域的 Blob
Azure 内容分发网络 (CDN) 现在支持针对自定义域名使用 HTTPS。 可以利用此功能通过 HTTPS 访问使用自定义域的存储 Blob。 为此，首先需要在 blob 或 Web 终结点上启用 Azure CDN，并将 CDN 映射到自定义域名。 执行这些步骤后，借助一键式启用、完善的证书管理，为自定义域启用 HTTPS 的流程得以简化，并且所有这些都包含在常规 CDN 定价中，不会额外增加成本。

此功能非常重要，因为可以使用它在传输敏感 Web 应用程序数据时保护其隐私和数据完整性。 使用 SSL 协议通过 HTTPS 为通信提供服务可以确保在通过 Internet 发送数据时数据是加密的。 HTTPS 提供了信任和身份验证，并保护 Web 应用程序免受攻击。

> [!NOTE]  
> 除了为自定义域名提供 SSL 支持外，Azure CDN 还可以帮助你扩展应用程序以便在世界范围内交付高带宽内容。 若要了解详细信息，请查看 [Azure CDN 概述](../../cdn/cdn-overview.md)。

## <a name="quick-start"></a>快速入门
下面是为自定义 Blob 存储终结点启用 HTTPS 时需要执行的步骤：

1.  [将 Azure 存储帐户与 Azure CDN 相集成](../../cdn/cdn-create-a-storage-account-with-cdn.md)。
    本文指导完成在 Azure 门户中创建存储帐户（如果尚未执行此操作）。

    > [!NOTE]  
    > 在 Azure 存储支持静态网站处于预览阶段期间，选择“源类型”下拉菜单中的“自定义源”，以添加存储 Web 终结点。 在 Azure 门户中，需要通过 CDN 配置文件（而不是直接在存储帐户中）这样做。

2.  [将 Azure CDN 内容映射到自定义域](../../cdn/cdn-map-content-to-custom-domain.md)。
3.  [在 Azure CDN 自定义域上启用 HTTPS](../../cdn/cdn-custom-ssl.md)。

## <a name="shared-access-signatures"></a>共享访问签名
如果 Blob 存储终结点配置为不允许进行匿名读取访问，则需要在向自定义域发出的每个请求中提供一个[共享访问签名 (SAS)](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 令牌。 默认情况下，Blob 存储终结点不允许进行匿名读取访问。 有关共享访问签名的详细信息，请参阅[管理对容器和 Blob 的匿名读取访问](storage-manage-access-to-resources.md)。

Azure CDN 不会遵守添加到 SAS 令牌的任何限制。 例如，所有 SAS 令牌都有一个过期时间。 这意味着仍然可以使用过期的 SAS 访问该内容，直至从 CDN 边缘节点中清除该内容。 可以通过设置缓存响应标头来控制在 CDN 上将数据缓存多长时间。 有关说明，请参阅[管理 Azure CDN 中的 Azure 存储 Blob 的过期](../../cdn/cdn-manage-expiration-of-blob-content.md)。

如果为同一 Blob 终结点创建了多个 SAS URL，建议为 Azure CDN 启用查询字符串缓存。 这可以确保每个 URL 都被视为一个唯一的实体。 有关详细信息，请参阅[控制 Azure CDN 对查询字符串的缓存行为](../../cdn/cdn-query-string.md)。

## <a name="http-to-https-redirection"></a>HTTP 到 HTTPS 重定向
可以选择将 HTTP 流量重定向到 HTTPS。 这需要使用 Verizon 提供的 Azure CDN 高级产品/服务。 需要[使用 Azure CDN 规则引擎来替代 HTTP 行为](../../cdn/cdn-rules-engine.md)并采用以下规则：

![](./media/storage-https-custom-domain-cdn/redirect-to-https.png)

“Cdn-endpoint-name”是指为 CDN 终结点配置的名称。 可以从下拉列表中选择此值。 “Origin-path”是指源存储帐户内静态内容所在的路径。 若要在一个容器中托管所有静态内容，请将“origin-path”替换为相应容器的名称。

若要更深入地了解规则，请参阅 [Azure CDN 规则引擎功能](../../cdn/cdn-rules-engine-reference-features.md)。

## <a name="pricing-and-billing"></a>定价和计费
当通过 Azure CDN 访问 Blob 时，将为边缘节点与原始存储（Blob 存储）之间的流量支付 [Blob 存储价格](https://azure.microsoft.com/pricing/details/storage/blobs/)，为从边缘节点访问的数据支付 [CDN 价格](https://azure.microsoft.com/pricing/details/cdn/)。

例如，假设你在美国西部有一个使用 Azure CDN 进行访问的存储帐户。 如果英国的某人尝试通过 CDN 访问该存储帐户中的某个 Blob，则 Azure 会首先在距离英国最近的边缘节点中查找该 Blob。 如果找到，则它将访问该 Blob 副本并使用 CDN 定价，因为它是在 CDN 上访问的该副本。 如果未找到，则 Azure 会将该 blob 复制到边缘节点（这会产生在 Blob 存储定价中指定的出口和事务费用），然后会在边缘节点上访问该文件（这会产生 CDN 计费）。

查看 [CDN 定价页](https://azure.microsoft.com/pricing/details/cdn/)时，请注意，对自定义域名的 HTTPS 支持仅适用于来自 Verizon 产品的 Azure CDN（标准版和高级版）。

## <a name="next-steps"></a>后续步骤
* [为 Blob 存储终结点配置自定义域名](storage-custom-domain-name.md)
* [Azure 存储中的静态网站托管（预览版）](storage-blob-static-website.md)
