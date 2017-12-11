---
title: "将自定义域添加到 CDN 终结点 | Microsoft Docs"
description: "了解如何将 Azure CDN 内容映射到自定义域。"
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 289f8d9e-8839-4e21-b248-bef320f9dbfc
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/09/2017
ms.author: mazha
ms.openlocfilehash: fd36b94c64ad31064dbb2e0badceaee5e5bc400f
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2017
---
# <a name="add-a-custom-domain-to-your-cdn-endpoint"></a>将自定义域添加到 CDN 终结点
创建配置文件后，通常还会创建一个或多个 CDN [终结点](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint)（`azureedge.net` 的子域），以通过 HTTP 和 HTTPS 传送内容。 默认情况下，此终结点会包含在所有 URL 中，例如 `https://contoso.azureedge.net/photo.png`。 为方便起见，Azure CDN 允许将自定义域（例如 `www.contoso.com`）与终结点相关联。 借助此选项，可以使用自定义域而不是终结点来传送内容。 例如，如果出于品牌宣传目的，你希望客户能够看到你自己的域名，则选项就很有用。

如果没有自定义域，则必须先在域提供商那里购买一个。 获取自定义域后，请执行以下步骤：
1. [访问域提供商的 DNS 记录](#step-1-access-dns-records-by-using-your-domain-provider)
2. [创建 CNAME DNS 记录](#step-2-create-the-cname-dns-records)
    - 选项 1：将自定义域直接映射到 CDN 终结点
    - 选项 2：使用 cdnverify 将自定义域映射到 CDN 终结点 
3. [在 Azure 中启用 CNAME 记录映射](#step-3-enable-the-cname-record-mapping-in-azure)
4. [验证该自定义子域引用 CDN 终结点](#step-4-verify-that-the-custom-subdomain-references-your-cdn-endpoint)
5. [（相关步骤）将永久自定义域映射到 CDN 终结点](#step-5-dependent-step-map-the-permanent-custom-domain-to-the-cdn-endpoint)

## <a name="step-1-access-dns-records-by-using-your-domain-provider"></a>步骤 1：通过域提供商访问 DNS 记录

如果使用 Azure 托管 [DNS 域](https://docs.microsoft.com/en-us/azure/dns/dns-overview)，必须将域提供商的 DNS 委托给 Azure DNS。 有关详细信息，请参阅[将域委托给 Azure DNS](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns)。

否则，如果通过域提供商处理 DNS 域，请登录到域提供商的网站。 查阅提供商的文档，或者在网站中搜索标有“域名”、“DNS”或“名称服务器管理”的区域，找到用于管理 DNS 记录的页面。 通常通过查看帐户信息，然后查找如“我的域”之类的链接，便可以找到 DNS 记录页面。 某些提供商提供不同的链接来添加不同的记录类型。

> [!NOTE]
> 对于某些提供商（例如 GoDaddy），在你选择单独的“保存更改”链接之前，这些 DNS 记录不会生效。 


## <a name="step-2-create-the-cname-dns-records"></a>步骤 2：创建 CNAME DNS 记录

在将自定义域与 Azure CDN 终结点结合使用之前，必须先在域提供商的配合下创建一个规范名称 (CNAME) 记录。 CNAME 记录是域名系统 (DNS) 中的一种记录类型，可通过指定“规范”或真实域名的别名域名，将源域映射到目标域。 对 Azure CDN 而言，源域是自定义域（和子域），目标域是 CDN 终结点。 通过门户或 API 将自定义域添加到终结点时，Azure CDN 会验证 CNAME DNS 记录。 

CNAME 记录映射特定的域和子域，例如 `www.contoso.com` 或 `cdn.contoso.com`；它无法将 CNAME 记录映射到根域，例如 `contoso.com`。 一个子域只能与一个 CDN 终结点关联，创建的 CNAME 记录会将其目标为子域的所有流量路由到指定的终结点。 例如，如果将 `www.contoso.com` 与 CDN 终结点关联在一起，则不能将它与其他 Azure 终结点（例如，存储帐户终结点或云服务终结点）相关联。 但是，可以将同一域中的不同子域用于不同的服务终结点。 还可以将不同子域映射到同一 CDN 终结点。

使用以下选项之一将自定义域映射到 CDN 终结点：

- 选项 1：直接映射。 如果自定义域上未运行生产流量，则可以直接将自定义域映射到 CDN 终结点。 将自定义域映射到 CDN 终结点的过程可能会导致在 Azure 门户中注册域时出现短暂的停机。 CNAME 映射条目应采用以下格式： 
 
  | 名称             | 类型  | 值                  |
  |------------------|-------|------------------------|
  | www\.contoso.com | CNAME | contoso\.azureedge.net |


- 选项 2：使用 **cdnverify** 子域进行映射。 如果自定义域上运行了不可中断的生产流量，可以创建 CNAME 到 CDN 终结点的临时映射。 借助此选项，可以使用 Azure **cdnverify** 子域来提供中间注册步骤，这样，在发生 DNS 映射时，用户仍可以持续访问你的域。

   1. 创建一个新的 CNAME 记录，并提供包含 **cdnverify** 子域的子域别名。 例如，**cdnverify.www** 或 **cdnverify.cdn**。 
   2. 使用以下格式提供主机名，即 CDN 终结点：`cdnverify.<EndpointName>.azureedge.net`。 CNAME 映射条目应采用以下格式： 

   | 名称                       | 类型  | 值                            |
   |----------------------------|-------|----------------------------------|
   | cdnverify.www\.contoso.com | CNAME | cdnverify.contoso\.azureedge.net | 


## <a name="step-3-enable-the-cname-record-mapping-in-azure"></a>步骤 3：在 Azure 中启用 CNAME 记录映射

使用前面的过程之一注册自定义域后，可在 Azure CDN 中启用自定义域功能。 

1. 登录到 [Azure 门户](https://portal.azure.com/)，浏览到 CDN 配置文件，其中包含需要映射到自定义域的终结点。  
2. 在“CDN 配置文件”边栏选项卡中，选择想要将子域关联到的 CDN 终结点。
3. 在终结点边栏选项卡的左上角，单击“自定义域”。 

   ![自定义域按钮](./media/cdn-map-content-to-custom-domain/cdn-custom-domain-button.png)

4. 在“自定义主机名”文本框中，输入自定义域，包括子域。 例如 `www.contoso.com` 或 `cdn.contoso.com`。

   ![添加自定义域的对话框](./media/cdn-map-content-to-custom-domain/cdn-add-custom-domain-dialog.png)

5. 单击“添加”。

   Azure 会验证所输入的域名是否存在 CNAME 记录。 如果该 CNAME 正确，会验证自定义域。 将 CNAME 记录传播到名称服务器可能需要一段时间。 如果域并没有立即获得验证，请验证 CNAME 记录是正确，然后等待几分钟重试。 对于**来自 Verizon 的 Azure CDN**（标准和高级）终结点，最长可能需要 90 分钟才能将自定义域的设置传播到所有 CDN 边缘节点。  


## <a name="step-4-verify-that-the-custom-subdomain-references-your-cdn-endpoint"></a>步骤 4：验证自定义子域是否引用 CDN 终结点

完成自定义域的注册后，请验证该自定义子域是否引用 CDN 终结点。
 
1. 确保在终结点缓存了公共内容。 例如，如果 CDN 终结点与某个存储帐户相关联，则 CDN 会将内容缓存在公共 blob 容器中。 若要测试自定义域，请验证是否已将容器设置为允许公共访问，并至少包含一个 Blob。

2. 在浏览器中，使用自定义域导航到 Blob 的地址。 例如，如果自定义域为 `cdn.contoso.com`，则缓存的 Blob 的 URL 应类似于：`http://cdn.contoso.com/mypubliccontainer/acachedblob.jpg`。


## <a name="step-5-dependent-step-map-the-permanent-custom-domain-to-the-cdn-endpoint"></a>步骤 5（相关步骤）：将永久自定义域映射到 CDN 终结点

此步骤与步骤 2 的选项 2（使用 **cdnverify** 子域进行映射）相关。 如果使用的是 **cdnverify** 临时子域并确认该域可正常工作，则可以将永久自定义域映射到 CDN 终结点。

1. 在域提供商的网站上，创建一条 CNAME DNS 记录，用于将永久自定义域映射到 CDN 终结点。 CNAME 映射条目应采用以下格式： 
 
   | 名称             | 类型  | 值                  |
   |------------------|-------|------------------------|
   | www\.contoso.com | CNAME | contoso\.azureedge.net |
2. 删除包含前面所创建的 **cdnverify** 子域的 CNAME 记录。

## <a name="see-also"></a>另请参阅
[如何为 Azure 启用内容交付网络 (CDN)](cdn-create-new-endpoint.md)  
[将域委托给 Azure DNS](../dns/dns-domain-delegation.md)
