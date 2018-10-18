---
title: 教程 - 通过 HTTPS 使用 Azure CDN 自定义域访问存储 Blob | Microsoft Docs
description: ''
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 7aaf4be23c806dda621430c4d1b0c142f41feb1f
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2018
ms.locfileid: "49090376"
---
# <a name="tutorial-access-storage-blobs-using-an-azure-cdn-custom-domain-over-https"></a>教程：通过 HTTPS 使用 Azure CDN 自定义域访问存储 Blob

将 Azure 存储帐户与 Azure 内容分发网络 (CDN) 集成以后，即可添加自定义域并在该域上为自定义 Blob 存储终结点启用 HTTPS。 

## <a name="prerequisites"></a>先决条件

在完成本教程中的步骤之前，必须先将 Azure 存储帐户与 Azure CDN 集成。 有关详细信息，请参阅[快速入门：将 Azure 存储帐户与 Azure CDN 集成](cdn-create-a-storage-account-with-cdn.md)。

## <a name="add-a-custom-domain"></a>添加自定义域
在配置文件中创建 CDN 终结点以后，终结点名称（azureedge.net 的子域）就会默认包括在用于交付 CDN 内容的 URL 中。 也可将自定义域与 CDN 终结点相关联。 使用此选项时，请在 URL 中使用自定义域而不是终结点名称来交付内容。 若要向终结点添加自定义域，请按[将自定义域添加到 Azure CDN 终结点](cdn-map-content-to-custom-domain.md)教程中的说明操作。

## <a name="configure-https"></a>配置 HTTPS
通过在自定义域上使用 HTTPS 协议，可以确保数据在 Internet 中通过 TLS/SSL 加密安全地进行分发。 Web 浏览器通过 HTTPS 连接到网站时，它会验证网站的安全证书并验证该证书是否是由合法的证书颁发机构颁发的。 若要在自定义域上配置 HTTPS，请按[在 Azure CDN 自定义域上配置 HTTPS](cdn-custom-ssl.md) 教程中的说明操作。

## <a name="shared-access-signatures"></a>共享访问签名
如果 Blob 存储终结点配置为不允许进行匿名读取访问，则应在向自定义域发出的每个请求中提供一个[共享访问签名 (SAS)](cdn-sas-storage-support.md) 令牌。 默认情况下，Blob 存储终结点不允许进行匿名读取访问。 有关 SAS 的详细信息，请参阅[管理对容器和 Blob 的匿名读取访问](../storage/blobs/storage-manage-access-to-resources.md)。

Azure CDN 会忽略添加到 SAS 令牌的任何限制。 例如，所有 SAS 令牌都有过期时间，这意味着仍然可以使用过期的 SAS 访问该内容，直至从 CDN 接入点 (POP) 服务器中清除该内容。 可以通过设置缓存响应标头来控制数据在 Azure CDN 上的缓存时间。 有关详细信息，请参阅[管理 Azure CDN 中的 Azure 存储 Blob 的过期](cdn-manage-expiration-of-blob-content.md)。

如果为同一 Blob 终结点创建了多个 SAS URL，请考虑启用查询字符串缓存。 这样做可以确保每个 URL 都被视为一个唯一的实体。 有关详细信息，请参阅[控制 Azure CDN 对查询字符串的缓存行为](cdn-query-string.md)。

## <a name="http-to-https-redirection"></a>HTTP 到 HTTPS 重定向
可以选择将 HTTP 流量重定向到 HTTPS，只需使用 [Azure CDN 规则引擎](cdn-rules-engine.md)创建[URL 重定向规则](cdn-rules-engine-reference-features.md#url-redirect)即可。 此选项要求**来自 Verizon 的 Azure CDN Premium** 配置文件。 

![URL 重定向规则](./media/cdn-storage-custom-domain-https/cdn-url-redirect-rule.png)

在此规则中，*Cdn-endpoint-name* 是指为 CDN 终结点配置的名称，它可以从下拉列表中选择。 *origin-path* 的值是指源存储帐户内静态内容所在的路径。 若要在一个容器中托管所有静态内容，请将 *origin-path* 替换为相应容器的名称。

## <a name="pricing-and-billing"></a>定价和计费
通过 Azure CDN 访问 Blob 时，将按 [Blob 存储价格](https://azure.microsoft.com/pricing/details/storage/blobs/)为 POP 服务器与原始存储（Blob 存储）之间的流量付费，按 [Azure CDN 定价](https://azure.microsoft.com/pricing/details/cdn/)为从 POP 服务器访问的数据付费。

例如，如果你在美国有一个存储帐户，该帐户使用 Azure CDN 进行访问，而某人在欧洲尝试通过 Azure CDN 访问该存储帐户中的某个 Blob，则 Azure CDN 会先查看该 Blob 最靠近欧洲的 POP。 如果找到，则 Azure CDN 将访问该 Blob 的副本并使用 CDN 定价，因为是在 Azure CDN 上访问的该副本。 如果未找到，则 Azure CDN 会将该 Blob 复制到 POP 服务器（这会产生在 Blob 存储定价中指定的出口和事务费用），然后会在 POP 服务器上访问该文件（这会进行 Azure CDN 计费）。

## <a name="next-steps"></a>后续步骤
[教程：设置 Azure CDN 缓存规则](cdn-caching-rules-tutorial.md)




