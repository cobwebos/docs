---
title: 教程 - 将自定义域添加到 Azure CDN 终结点 | Microsoft Docs
description: 在本教程中，请将 Azure CDN 终结点内容映射到自定义域。
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/15/2018
ms.author: v-deasim
ms.custom: mvc
ms.openlocfilehash: d20dca9a1248cfb2915ebbc456fba80aadcf81f5
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
ms.locfileid: "34196498"
---
# <a name="tutorial-add-a-custom-domain-to-your-azure-cdn-endpoint"></a>教程：将自定义域添加到 Azure CDN 终结点
本教程介绍如何将自定义域添加到 Azure 内容分发网络 (CDN) 终结点。 使用 CDN 终结点来交付内容时，如果希望自己的域名在 CDN URL 中可见，则必须使用自定义域。 使用可见的域名可以方便客户，适用于推广品牌。 

在配置文件中创建 CDN 终结点以后，终结点名称（azureedge.net 的子域）就会默认包括在用于交付 CDN 内容的 URL（例如 https:\//contoso.azureedge.net/photo.png）中。 为方便起见，Azure CDN 提供了用于将自定义域与 CDN 终结点相关联的选项。 使用此选项时，请在 URL 中使用自定义域而不是终结点名称来交付内容（例如，https:\//www.contoso.com/photo.png）。 

本教程介绍如何执行下列操作：
> [!div class="checklist"]
> - 创建 CNAME DNS 记录。
> - 将自定义域与 CDN 终结点相关联。
> - 验证自定义域。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

在完成本教程中的步骤之前，必须先创建一个 CDN 配置文件，一个至少一个 CDN 终结点。 有关详细信息，请参阅[快速入门：创建 Azure CDN 配置文件和终结点](cdn-create-new-endpoint.md)。

如果没有自定义域，则必须先在域提供商那里购买一个。 有关示例，请参阅[购买自定义域名](https://docs.microsoft.com/azure/app-service/custom-dns-web-site-buydomains-web-app)。

如果使用 Azure 来托管 [DNS 域](https://docs.microsoft.com/azure/dns/dns-overview)，必须将域提供商的域名系统 (DNS) 委托给 Azure DNS。 有关详细信息，请参阅 [向 Azure DNS 委派域](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns)。 否则，如果通过域提供商处理 DNS 域，请转到[创建 CNAME DNS 记录](#create-a-cname-dns-record)。


## <a name="create-a-cname-dns-record"></a>创建 CNAME DNS 记录

在将自定义域与 Azure CDN 终结点结合使用之前，必须先在域提供商的配合下创建一个规范名称 (CNAME) 记录，使之指向 CDN 终结点。 CNAME 记录是一种 DNS 记录，用于将源域名映射到目标域名。 就 Azure CDN 来说，源域名是自定义域名，目标域名是 CDN 终结点主机名。 在 Azure CDN 验证已创建的 CNAME 记录以后，目标为源自定义域（例如 www.contoso.com）的流量就会路由到指定的目标 CDN 终结点主机名（例如 contoso.azureedge.net）。 

一个自定义域及其子域一次只能与一个终结点相关联。 但是，可以通过使用多个 CNAME 记录，将同一自定义域中的不同子域用于不同的 Azure 服务终结点。 还可以将包含不同子域的自定义域映射到同一 CDN 终结点。


## <a name="map-the-temporary-cdnverify-subdomain"></a>映射临时的 cdnverify 子域

映射现有的生产环境中的域时，有一些特殊注意事项。 在 Azure 门户中注册自定义域时，该域可能会出现短暂的停机现象。 为了避免 Web 流量中断，请先将自定义域映射到包含 Azure cdnverify 子域的 CDN 终结点主机名，以便创建临时的 CNAME 映射。 使用此方法，用户可以在进行 DNS 映射时访问域，不会出现中断现象。 

否则，如果你是第一次使用自定义域，其上没有生产流量在运行，则可直接将自定义域映射到 CDN 终结点。 请转到[映射永久自定义域](#map-permanent-custom-domain)。

若要使用 cdnverify 子域创建 CNAME 记录，请执行以下操作：

1. 登录到自定义域的域提供商的网站。

2. 查阅提供商的文档，或者在网站中搜索标有“域名”、“DNS”或“名称服务器管理”的区域，找到用于管理 DNS 记录的页面。 

3. 为自定义域创建一个 CNAME 记录条目并完成各个字段，如下表所示（字段名称可能有所不同）：

    | Source                    | Type  | 目标                     |
    |---------------------------|-------|---------------------------------|
    | cdnverify.www.contoso.com | CNAME | cdnverify.contoso.azureedge.net |

    - 源：采用 cdnverify._&lt;自定义域名&gt; 格式输入自定义域名，包括 cdnverify 子域。 例如，cdnverify.www.contoso.com。

    - 类型：输入 *CNAME*。

    - 目标：采用 cdnverify.&lt;终结点名称&gt;.azureedge.net 格式输入 CDN 终结点主机名，包括 cdnverify 子域。 例如，cdnverify.contoso.azureedge.net。

4. 保存所做更改。

例如，GoDaddy 域注册机构的过程如下：

1. 登录后选择要使用的自定义域。

2. 在“域”部分选择“管理所有项”，然后选择“DNS” | “管理区域”。

3. 对于“域名”，请输入自定义域，然后选择“搜索”。

4. 在“DNS 管理”页中选择“添加”，然后在“类型”列表中选择“CNAME”。

5. 完成 CNAME 条目的以下字段：

    ![CNAME 条目](./media/cdn-map-content-to-custom-domain/cdn-cdnverify-cname-entry.png)

    - 类型：让 *CNAME* 保持选中状态。

    - 主机：输入要使用的自定义域的子域，包括 cdnverify 子域名称。 例如，cdnverify.www。

    - 指向：输入 CDN 终结点的主机名，包括 cdnverify 子域名称。 例如，cdnverify.contoso.azureedge.net。 

    - TTL：让“1 小时”保持选中状态。

6. 选择“保存”。
 
    此时会将 CNAME 条目添加到 DNS 记录表。

    ![DNS 记录表](./media/cdn-map-content-to-custom-domain/cdn-cdnverify-dns-table.png)


## <a name="associate-the-custom-domain-with-your-cdn-endpoint"></a>将自定义域与 CDN 终结点相关联

注册自定义域以后，即可将其添加到 CDN 终结点。 

1. 登录到 [Azure 门户](https://portal.azure.com/)，浏览到 CDN 配置文件，其中包含需要映射到自定义域的终结点。
    
2. 在“CDN 配置文件”页上，选择要与自定义域关联的 CDN 终结点。

   此时会打开“终结点”页。
    
3. 选择“自定义域”。 

   ![CDN“自定义域”按钮](./media/cdn-map-content-to-custom-domain/cdn-custom-domain-button.png)

   此时会打开“添加自定义域”页。

4. 对于**终结点主机名**，用作 CNAME 记录的目标域的终结点主机名已预先填好，并源自 CDN 终结点 URL：*&lt;终结点主机名&gt;*.azureedge.net。 无法进行更改。

5. 对于**自定义主机名**，请输入自定义域（包括子域），以用作 CNAME 记录的源域。 例如，www.contoso.com 或 cdn.contoso.com。请勿使用 cdnverify 子域名称。

   ![CDN“自定义域”对话框](./media/cdn-map-content-to-custom-domain/cdn-add-custom-domain.png)

6. 选择 **添加** 。

   Azure 会验证所输入的自定义域名是否存在 CNAME 记录。 如果该 CNAME 正确，会验证自定义域。 

   新的自定义域设置传播到所有 CDN 边缘节点可能需要一些时间： 
    - 对于 Microsoft 的 Azure CDN 标准版配置文件，传播通常可在 10 分钟内完成。 
    - 对于 **Akamai 的 Azure CDN 标准版**配置文件，传播通常可在一分钟内完成。 
    - 对于 Verizon 的 Azure CDN 标准版和 Verizon 的 Azure CDN 高级版配置文件，传播通常可在 90 分钟内完成。   


## <a name="verify-the-custom-domain"></a>验证自定义域

完成自定义域的注册后，请验证该自定义域是否引用 CDN 终结点。
 
1. 确保在终结点缓存了公共内容。 例如，如果 CDN 终结点与某个存储帐户相关联，则 Azure CDN 会将内容缓存在公共容器中。 若要测试自定义域，请验证容器是否已设置为允许公共访问，并至少包含一个文件。

2. 在浏览器中，使用自定义域导航到文件的地址。 例如，如果自定义域为 cdn.contoso.com，则指向已缓存文件的 URL 应该类似于以下 URL：http:\//cdn.contoso.com/my-public-container/my-file.jpg。 验证结果是否与直接在 *&lt;终结点主机名&gt;*.azureedge.net 上访问 CDN 终结点时的结果相同。


## <a name="map-the-permanent-custom-domain"></a>映射永久自定义域

如果已验证 cdnverify 子域已成功映射到终结点（或者如果使用的是不在生产环境中的新自定义域），则可将自定义域直接映射到 CDN 终结点主机名。

若要创建自定义域的 CNAME 记录，请执行以下操作：

1. 登录到自定义域的域提供商的网站。

2. 查阅提供商的文档，或者在网站中搜索标有“域名”、“DNS”或“名称服务器管理”的区域，找到用于管理 DNS 记录的页面。 

3. 为自定义域创建一个 CNAME 记录条目并完成各个字段，如下表所示（字段名称可能有所不同）：

    | Source          | Type  | 目标           |
    |-----------------|-------|-----------------------|
    | www.contoso.com | CNAME | contoso.azureedge.net |

    - 源：输入自定义域名（例如 www.contoso.com）。

    - 类型：输入 *CNAME*。

    - 目标：输入 CDN 终结点主机名。 必须采用 _&lt;endpoint name&gt;_.azureedge.net 格式， 例如 contoso.azureedge.net。

4. 保存所做更改。

5. 如果此前已创建临时的 cdnverify 子域 CNAME 记录，请将其删除。 

6. 如果是在生产环境中第一次使用此自定义域，请按[将自定义域与 CDN 终结点相关联](#associate-the-custom-domain-with-your-cdn-endpoint)和[验证自定义域](#verify-the-custom-domain)所述步骤进行操作。

例如，GoDaddy 域注册机构的过程如下：

1. 登录后选择要使用的自定义域。

2. 在“域”部分选择“管理所有项”，然后选择“DNS” | “管理区域”。

3. 对于“域名”，请输入自定义域，然后选择“搜索”。

4. 在“DNS 管理”页中选择“添加”，然后在“类型”列表中选择“CNAME”。

5. 完成 CNAME 条目的字段：

    ![CNAME 条目](./media/cdn-map-content-to-custom-domain/cdn-cname-entry.png)

    - 类型：让 *CNAME* 保持选中状态。

    - 主机：输入要使用的自定义域的子域， 例如 www 或 cdn。

    - 指向：输入 CDN 终结点的主机名， 例如 contoso.azureedge.net。 

    - TTL：让“1 小时”保持选中状态。

6. 选择“保存”。
 
    此时会将 CNAME 条目添加到 DNS 记录表。

    ![DNS 记录表](./media/cdn-map-content-to-custom-domain/cdn-dns-table.png)

7. 如果有一个 cdnverify CNAME 记录，请选择其旁边的铅笔图标，然后选择垃圾桶图标。

8. 选择“删除”，以便删除 CNAME 记录。


## <a name="clean-up-resources"></a>清理资源

在前述步骤中，已将自定义域添加到 CDN 终结点。 如果不再需要将终结点与自定义域相关联，可通过以下步骤删除自定义域：
 
1. 在 CDN 配置文件中，选择其自定义域需要删除的终结点。

2. 在“终结点”页的“自定义域”下，右键单击要删除的自定义域，然后从上下文菜单中选择“删除”。  

   自定义域于是与 CDN 终结点解除关联。


## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> - 创建 CNAME DNS 记录。
> - 将自定义域与 CDN 终结点相关联。
> - 验证自定义域。

转到下一教程，了解如何在 Azure CDN 自定义域上配置 HTTPS。

> [!div class="nextstepaction"]
> [教程：在 Azure CDN 自定义域上配置 HTTPS](cdn-custom-ssl.md)


