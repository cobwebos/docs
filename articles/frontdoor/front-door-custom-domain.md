---
title: 教程 - 将自定义域添加到 Azure Front Door 配置 | Microsoft Docs
description: 本教程介绍如何将自定义域载入 Azure Front Door。
services: frontdoor
documentationcenter: ''
author: sharad4u
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 8106c68397dea8d52c6d2daa2d09dfbc72c2a4c8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46995051"
---
# <a name="tutorial-add-a-custom-domain-to-your-front-door"></a>教程：将自定义域添加到 Front Door
本教程介绍如何将自定义域添加到 Front Door。 使用 Azure Front Door 服务进行应用程序交付时，如果希望自己的域名在最终用户请求中可见，则必须使用自定义域。 使用可见的域名可以方便客户，适用于推广品牌。

创建 Front Door 后，URL 中将包含默认前端主机（`azurefd.net` 的子域），以便在默认情况下从后端交付 Front Door 内容（例如 https:\//contoso.azurefd.net/activeusers.htm）。 为方便起见，Azure Front Door 提供了用于将自定义域与默认主机相关联的选项。 通过此选项，可在 URL 中使用自定义域而不是 Front Door 拥有的域名来交付内容（例如 https:\//www.contoso.com/photo.png）。 

本教程介绍如何执行下列操作：
> [!div class="checklist"]
> - 创建 CNAME DNS 记录。
> - 将自定义域与 Front Door 相关联。
> - 验证自定义域。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

在完成本教程中的步骤之前，必须先创建一个 Front Door。 有关更多信息，请参阅[快速入门：创建 Front Door](quickstart-create-front-door.md)。

如果没有自定义域，则必须先在域提供商那里购买一个。 有关示例，请参阅[购买自定义域名](https://docs.microsoft.com/azure/app-service/custom-dns-web-site-buydomains-web-app)。

如果使用 Azure 来托管 [DNS 域](https://docs.microsoft.com/azure/dns/dns-overview)，必须将域提供商的域名系统 (DNS) 委托给 Azure DNS。 有关详细信息，请参阅 [向 Azure DNS 委派域](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns)。 否则，如果通过域提供商处理 DNS 域，请转到[创建 CNAME DNS 记录](#create-a-cname-dns-record)。


## <a name="create-a-cname-dns-record"></a>创建 CNAME DNS 记录

在将自定义域与 Front Door 结合使用之前，必须先在域提供商的配合下创建一个规范名称 (CNAME) 记录，使之指向Front Door 的默认前端主机（例如 contose.azurefd.net）。 CNAME 记录是一种 DNS 记录，用于将源域名映射到目标域名。 对于 Azure Front Door 服务，源域名是自定义域名，目标域名是 Front Door 默认主机名。 在 Front Door 验证了创建的 CNAME 记录后，发往源自定义域（如 www.contoso.com）的流量将路由到指定的目标 Front Door 默认主机名（如 contoso.azurefd.net）。 

自定义域及其子域一次只能与一个 Front Door 相关联。 但是，可以通过使用多个 CNAME 记录，将同一自定义域中的不同子域用于不同的 Front Door。 还可以将包含不同子域的自定义域映射到同一 Front Door。


## <a name="map-the-temporary-afdverify-sub-domain"></a>映射临时 afdverify 子域

映射现有的生产环境中的域时，有一些特殊注意事项。 在 Azure 门户中注册自定义域时，该域可能会出现短暂的停机现象。 为避免 Web 流量中断，请先将自定义域映射到包含 Azure afdverify 子域的 Front Door 默认前端主机名，以便创建临时的 CNAME 映射。 使用此方法，用户可以在进行 DNS 映射时访问域，不会出现中断现象。

此外，如果你是第一次使用自定义域，其上未运行任何生产流量，则可直接将自定义域映射到 CDN 终结点。 前进到[映射永久自定义域](#map-the-permanent-custom-domain)。

若要使用 afdverify 子域创建 CNAME 记录，请执行以下操作：

1. 登录到你的自定义域的域提供商的网站。

2. 查阅提供商的文档，或者在网站中搜索标有“域名”、“DNS”或“名称服务器管理”的区域，找到用于管理 DNS 记录的页面。 

3. 为自定义域创建一个 CNAME 记录条目并完成各个字段，如下表所示（字段名称可能有所不同）：

    | Source                    | Type  | 目标                     |
    |---------------------------|-------|---------------------------------|
    | afdverify.www.contoso.com | CNAME | afdverify.contoso.azurefd.net |

    - 源：采用 afdverify.&lt;自定义域名&gt; 格式输入自定义域名，包括 afdverify 子域。 例如 afdverify.www.contoso.com。

    - 类型：输入 *CNAME*。

    - 目标：采用 afdverify.&lt;终结点名称&gt;.azurefd.net 格式输入默认 Front Door 前端主机，包括 afdverify 子域。 例如 afdverify.contoso.azurefd.net。

4. 保存所做更改。

例如，GoDaddy 域注册机构的过程如下：

1. 登录后选择要使用的自定义域。

2. 在“域”部分选择“管理所有项”，然后选择“DNS” | “管理区域”。

3. 对于“域名”，请输入自定义域，然后选择“搜索”。

4. 在“DNS 管理”页中选择“添加”，然后在“类型”列表中选择“CNAME”。

5. 完成 CNAME 条目的以下字段：

    - 类型：让 *CNAME* 保持选中状态。

    - 主机：输入要使用的自定义域的子域，包括 afdverify 子域名称。 例如 afdverify.www。

    - 指向：输入默认 Front Door 前端主机的主机名，包括 afdverify 子域名称。 例如 afdverify.contoso.azurefd.net。 

    - TTL：让“1 小时”保持选中状态。

6. 选择“保存”。
 
    此时会将 CNAME 条目添加到 DNS 记录表。


## <a name="associate-the-custom-domain-with-your-front-door"></a>将自定义域与 Front Door 相关联

注册自定义域以后，即可将其添加到 Front Door。

1. 登录到 [Azure 门户](https://portal.azure.com/)，浏览到 Front Door，其中包含需要映射到自定义域的前端主机。
    
2. 在“Front Door 设计器”页上，单击“+”添加自定义域。
    
3. 指定“自定义域”。 

4. 对于“前端主机”，用作 CNAME 记录的目标域的前端主机已预先填充，并派生自 Front Door ：&lt;默认主机名&gt;.azurefd.net。 无法进行更改。

5. 对于**自定义主机名**，请输入自定义域（包括子域），以用作 CNAME 记录的源域。 例如，www.contoso.com 或 cdn.contoso.com。 请勿使用 afdverify 子域名称。

6. 选择 **添加** 。

   Azure 会验证所输入的自定义域名是否存在 CNAME 记录。 如果该 CNAME 正确，会验证自定义域。

>[!WARNING]
> 必须确保 Front Door 中的每个前端主机（包括自定义域）都具有传递规则，且其中包含与之关联的默认路径 ('/\*')。 也就是说，在所有传递规则中，默认路径 ('/\*') 中定义的每个前端主机必须至少有一个路由规则。 如果不这样做，可能会导致最终用户流量无法正确路由。

## <a name="verify-the-custom-domain"></a>验证自定义域

完成自定义域的注册后，请验证该自定义域是否引用默认 Front Door 前端主机。
 
在浏览器中，使用自定义域导航到文件的地址。 例如，如果自定义域为 robotics.contoso.com，则指向已缓存文件的 URL 应该类似于以下 URL：http:\//robotics.contoso.com/my-public-container/my-file.jpg。 验证其结果是否与直接从 &lt;Front Door 主机&gt;.azurefd.net 访问 Front Door 的结果相同。


## <a name="map-the-permanent-custom-domain"></a>映射永久自定义域

如果确认 afdverify 子域已成功映射到 Front Door（或者如果使用的是不在生产环境中的新自定义域），则可将自定义域直接映射到默认 Front Door 前端主机。

若要创建自定义域的 CNAME 记录，请执行以下操作：

1. 登录到你的自定义域的域提供商的网站。

2. 查阅提供商的文档，或者在网站中搜索标有“域名”、“DNS”或“名称服务器管理”的区域，找到用于管理 DNS 记录的页面。 

3. 为自定义域创建一个 CNAME 记录条目并完成各个字段，如下表所示（字段名称可能有所不同）：

    | Source          | Type  | 目标           |
    |-----------------|-------|-----------------------|
    | www.contoso.com | CNAME | contoso.azurefd.net |

    - 源：输入自定义域名（例如 www.contoso.com）。

    - 类型：输入 *CNAME*。

    - 目标：输入默认 Front Door 前端主机。 必须采用 &lt;主机名&gt;.azurefd.net 格式。 例如 contoso.azurefd.net.

4. 保存所做更改。

5. 如果此前已创建临时的 afdverify 子域 CNAME 记录，请将其删除。 

6. 如果是在生产环境中首次使用此自定义域，请按[将自定义域与 Front Door 相关联](#associate-the-custom-domain-with-your-front-door)和[验证自定义域](#verify-the-custom-domain)中所述步骤进行操作。

例如，GoDaddy 域注册机构的过程如下：

1. 登录后选择要使用的自定义域。

2. 在“域”部分选择“管理所有项”，然后选择“DNS” | “管理区域”。

3. 对于“域名”，请输入自定义域，然后选择“搜索”。

4. 在“DNS 管理”页中选择“添加”，然后在“类型”列表中选择“CNAME”。

5. 完成 CNAME 条目的字段：

    - 类型：让 *CNAME* 保持选中状态。

    - 主机：输入要使用的自定义域的子域， 例如 www 或配置文件。

    - 指向：输入 Front Door 的默认主机名。 例如 contoso.azurefd.net. 

    - TTL：让“1 小时”保持选中状态。

6. 选择“保存”。
 
    此时会将 CNAME 条目添加到 DNS 记录表。

7. 如果有 afdverify CNAME 记录，请选择其旁边的铅笔图标，然后选择回收站图标。

8. 选择“删除”，以便删除 CNAME 记录。


## <a name="clean-up-resources"></a>清理资源

在前述步骤中，已将自定义域添加到 Front Door。 如果不再需要将 Front Door 与自定义域相关联，可通过以下步骤删除自定义域：
 
1. 在 Front Door 设计器中，选择要删除的自定义域。

2. 在自定义域的上下文菜单中单击“删除”。  

   自定义域于是与 CDN 终结点解除关联。


## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> - 创建 CNAME DNS 记录。
> - 将自定义域与 Front Door 相关联。
> - 验证自定义域。