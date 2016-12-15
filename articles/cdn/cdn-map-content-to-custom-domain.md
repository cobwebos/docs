---
title: "如何将 Azure 内容交付网络 (CDN) 内容映射到自定义域 | Microsoft Docs"
description: "本主题演示如何将 CDN 内容映射到自定义域。"
services: cdn
documentationcenter: 
author: camsoper
manager: erikre
editor: 
ms.assetid: 289f8d9e-8839-4e21-b248-bef320f9dbfc
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/28/2016
ms.author: casoper
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 567e72805c22c8100ef2d8d97c1e26a77f214ca3


---
# <a name="how-to-map-custom-domain-to-content-delivery-network-cdn-endpoint"></a>如何将自定义域映射到内容交付网络 (CDN) 终结点
可以将自定义域映射到 CDN 终结点，以便在指向已缓存内容的 URL 中使用自己的域名，而不是使用 azureedge.net 的子域。

可以使用两种方法将自定义域映射到 CDN 终结点：

1. [在域注册机构中创建一个 CNAME 记录，并将自定义域和子域映射到 CDN 终结点](#register-a-custom-domain-for-an-azure-cdn-endpoint)
   
    CNAME 记录是一种 DNS 功能，可将源域（如 `www.contosocdn.com` 或 `cdn.contoso.com`）映射到目标域。 在此情况下，源域是你的自定义域和子域（始终需要类似于 **www** 或 **cdn** 的子域）。 目标域是你的 CDN 终结点。  
   
    但是，将自定义域映射到 CDN 终结点的过程会导致你在 Azure 门户中注册域时出现短暂的停机时间。
2. [通过 cdnverify **添加中间注册步骤**](#register-a-custom-domain-for-an-azure-cdn-endpoint-using-the-intermediary-cdnverify-subdomain)
   
    如果你的自定义域目前所支持的应用程序的服务级别协议 (SLA) 要求不能有停机时间，则可以使用 Azure **cdnverify** 子域提供中间注册步骤，以便用户在 DNS 映射进行时能够访问你的域。  

使用上面的其中一个过程注册自定义域后，你需要[验证自定义子域可以引用 CDN 终结点](#verify-that-the-custom-subdomain-references-your-cdn-endpoint)。

> [!NOTE]
> 你必须在域注册机构中创建 CNAME 记录，将你的域映射到 CDN 终结点 CNAME 记录会映射特定子域，如 `www.contoso.com` 或 `cdn.contoso.com`。 无法将 CNAME 记录映射到根域，例如 `contoso.com`。
> 
> 只能将一个子域与一个 CDN 终结点相关联。 你创建的 CNAME 记录会将其目标为子域的所有流量路由到指定的终结点。  例如，如果将 `www.contoso.com` 与 CDN 终结点关联在一起，则不能将它与其他 Azure 终结点（例如，存储帐户终结点或云服务终结点）进行关联。 但是，你可以将同一域中的不同子域用于不同的服务终结点。 你还可以将不同子域映射到同一 CDN 终结点。
> 
> 对于**来自 Verizon 的 Azure CDN**（标准和高级） 终结点，请注意，它需要长达 **90 分钟**将自定义域更改传播到 CDN 边缘节点。
> 
> 

## <a name="register-a-custom-domain-for-an-azure-cdn-endpoint"></a>为 Azure CDN 终结点注册自定义域
1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 单击“**浏览**”，然后单击“**CDN 配置文件**”，然后选择需要映射到自定义域的具有终结点的 CDN 配置文件。  
3. 在“**CDN 配置文件**”边栏选项卡，单击想要将子域关联的 CDN 终结点。
4. 在终结点边栏选项卡页的顶部，单击“**添加自定义域**”按钮。  在“**添加自定义域**”边栏选项卡，你会看到从 CDN 终结点派生的终结点主机名，该名称可用于创建新的 CNAME 记录。 主机名地址的格式将显示为 **&lt;终结点名称>. azureedge.net**。  你可以复制此主机名，将其用于创建 CNAME 记录。  
5. 导航到域注册机构的网站，找到创建 DNS 记录的部分。 可能会在“**域名**”、“**DNS**”或“**名称服务器管理**”等部分中找到此页。
6. 找到用于管理 CNAME 的部分。 你可能需要转至高级设置页面，并找到“CNAME”、“别名”或“子域”字样。
7. 创建一个新的 CNAME 记录，将所选子域（例如 **www** 或 **cdn**）映射到在“**添加自定义域**”边栏选项卡中提供的主机名。
8. 返回到“**添加自定义域**”边栏选项卡，在对话框中输入自定义域（包括子域）。 例如，按格式 `www.contoso.com` 或 `cdn.contoso.com` 输入域名。   
   
   Azure 会验证你所输入的域名是否存在 CNAME 记录。 如果该 CNAME 正确，将验证你的自定义域。  但是，对于**来自 Verizon 的 Azure CDN**（标准和高级）终结点，它可能需要长达 90 分钟将自定义域的设置传播到所有 CDN 边缘节点。  
   
   请注意，在某些情况下，CNAME 记录传播到 Internet 上的名称服务器需要一定的时间。 如果你的域并没有立即获得验证，而你确信 CNAME 记录是正确的，则请等待数分钟，然后重试。

## <a name="register-a-custom-domain-for-an-azure-cdn-endpoint-using-the-intermediary-cdnverify-subdomain"></a>使用中间 cdnverify 子域为 Azure CDN 终结点注册自定义域
1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 单击“**浏览**”，然后单击“**CDN 配置文件**”，然后选择需要映射到自定义域的具有终结点的 CDN 配置文件。  
3. 在“**CDN 配置文件**”边栏选项卡，单击想要将子域关联的 CDN 终结点。
4. 在终结点边栏选项卡页的顶部，单击“**添加自定义域**”按钮。  在“**添加自定义域**”边栏选项卡，你会看到从 CDN 终结点派生的终结点主机名，该名称可用于创建新的 CNAME 记录。 主机名地址的格式将显示为 **&lt;终结点名称>. azureedge.net**。  你可以复制此主机名，将其用于创建 CNAME 记录。
5. 导航到域注册机构的网站，找到创建 DNS 记录的部分。 可能会在“**域名**”、“**DNS**”或“**名称服务器管理**”等部分中找到此页。
6. 找到用于管理 CNAME 的部分。 你可能需要转至高级设置页面，并找到“**CNAME**”、“**别名**”或“**子域**”字样。
7. 创建一个新的 CNAME 记录，并且提供包含 **cdnverify** 子域的子域别名。 例如，指定的子域将采用 **cdnverify.www** 或 **cdnverify.cdn** 格式。 然后提供主机名，这是你的 CDN 终结点，格式为 **cdnverify.&lt;终结点名称 >. azureedge.net**。
8. 返回到“**添加自定义域**”边栏选项卡，在对话框中输入自定义域（包括子域）。 例如，按格式 `www.contoso.com` 或 `cdn.contoso.com` 输入域名。 请注意在此步骤中，你无需为子域添加前缀 **cdnverify**。  
   
    Azure 会验证你所输入的 cdnverify 域名是否存在 CNAME 记录。
9. 此时，你的自定义域已由 Azure 进行了验证，但传输到你的域的流量尚未路由到你的 CDN 终结点。 在等待足够长的时间以允许自定义域设置传播到 CDN 边缘节点（对于**来自 Verizon 的 Azure CDN**，这需要 90 分钟，对于**来自 Akamai 的 Azure CDN**，这需要 1-2 分钟）后，返回到 DNS 注册机构网站，并创建另一个 CNAME 记录，将子域映射到 CDN 终结点。 例如，可将子域指定为 **www** 或 **cdn**，并将主机名指定为 **&lt;终结点名称>.azureedge.net**。 完成此步骤后，也就完成了你的自定义域的注册。
10. 最后，你可以使用 **cdnverify** 删除你创建的 CNAME 记录，因为在中间步骤才需要使用此记录。  

## <a name="verify-that-the-custom-subdomain-references-your-cdn-endpoint"></a>验证该自定义子域引用你的 CDN 终结点
* 完成自定义域的注册以后，你就可以通过自定义域访问缓存在 CDN 终结点的内容。
  首先，请确保你在终结点缓存了公共内容。 例如，如果你的 CDN 终结点与某个存储帐户相关联，则 CDN 会将内容缓存在公共 blob 容器中。 若要测试自定义域，请确保已将你的容器设置为允许公共访问，而且该容器包含至少一个 blob。
* 在浏览器中，导航到使用自定义域的 blob 的地址。 例如，如果你的自定义域为 `cdn.contoso.com`，则指向缓存的 blob 的 URL 将类似于以下 URL：http://cdn.contoso.com/mypubliccontainer/acachedblob.jpg

## <a name="see-also"></a>另请参阅
[如何对 Azure 启用内容交付网络 (CDN)](cdn-create-new-endpoint.md)  




<!--HONumber=Nov16_HO3-->


