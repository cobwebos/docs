---
title: "将 Azure CDN 内容映射到自定义域 |Microsoft 文档"
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
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: cd6db44f7776859d1e6a893543cf0666182ca41a
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="map-azure-cdn-content-to-a-custom-domain"></a>将 Azure CDN 内容映射到自定义域
为了给缓存的内容，而无需使用的子域 azureedge.net 在 URLs 中使用你自己的域名，可以将自定义域映射到 CDN 终结点。

有两种方法可以将自定义域映射到 CDN 终结点：

1. [在域注册机构创建一个 CNAME 记录，并将你的自定义域和子域映射到 CDN 终结点](#register-a-custom-domain-for-an-azure-cdn-endpoint)。
   
    CNAME 记录是一种 DNS 功能，将源域，如映射`www.contosocdn.com`或`cdn.contoso.com`，到目标域。 在这种情况下，源域是你的自定义域和子域 (子域，如**www**或**cdn**始终是必需)。 目标域是 CDN 终结点。  
   
    将你的自定义域映射到 CDN 终结点的过程，但是，会在出现短暂的停机时间域中在 Azure 门户中注册域时。
2. [添加中间注册步骤与**cdnverify**](#register-a-custom-domain-for-an-azure-cdn-endpoint-using-the-intermediary-cdnverify-subdomain)
   
    如果你的自定义域目前所支持的应用程序服务级别协议 (SLA) 要求没有停机时间，则可以使用 Azure **cdnverify**子域提供中间注册步骤，以便用户将能够访问你的域 DNS 映射正在进行时。  

注册自定义域使用上面的过程之一后，您将需要[验证自定义子域引用你的 CDN 终结点](#verify-that-the-custom-subdomain-references-your-cdn-endpoint)。

> [!NOTE]
> 必须在域注册机构将域映射到 CDN 终结点创建的 CNAME 记录。 CNAME 记录映射特定子域，如`www.contoso.com`或`cdn.contoso.com`。 不能将 CNAME 记录映射到根域，如`contoso.com`。
> 
> 只能与一个 CDN 终结点关联子域。 你创建的 CNAME 记录将路由到指定终结点子域进行寻址的所有流量。  例如，如果您将相关联`www.contoso.com`CDN 终结点，然后你不能将其与其他 Azure 终结点，例如存储帐户终结点或云服务终结点。 但是，可以为不同的服务终结点将同一域中使用不同的子域。 你还可以将不同子域映射到同一个 CDN 终结点。
> 
> 有关**Verizon 从 Azure CDN** （标准版和高级） 终结点，请注意，最多需要**90 分钟**的自定义域更改传播到 CDN 边缘节点。
> 
> 

## <a name="register-a-custom-domain-for-an-azure-cdn-endpoint"></a>注册为 Azure CDN 终结点的自定义域
1. 登录到[Azure 门户](https://portal.azure.com/)。
2. 单击**浏览**，然后**CDN 配置文件**，则需要将映射到自定义域与终结点的 CDN 配置文件。  
3. 在**的 CDN 配置文件**边栏选项卡，单击你想要将子域关联的 CDN 终结点。
4. 在终结点边栏选项卡的顶部，单击**添加自定义域**按钮。  在**添加自定义域**边栏选项卡，你将看到从 CDN 终结点，以便在创建新的 CNAME 记录使用派生的终结点主机名。 主机名地址的格式将显示为 **&lt;EndpointName >。 azureedge.net**。  你可以复制要在创建 CNAME 记录时使用此主机名。  
5. 导航到你的域注册机构的网站，并找到用于创建 DNS 记录的部分。 你可能这部分中找到如**域名**， **DNS**，或**名称服务器管理**。
6. 找到用于管理 Cname 的部分。 你可能需要转至高级的设置页面并查找字样，CNAME、 别名或子域。
7. 创建新的 CNAME 记录映射所选的子域 (例如， **www**或**cdn**) 中提供的主机名**添加自定义域**边栏选项卡。 
8. 返回到**添加自定义域**边栏选项卡，然后输入你的自定义域，包括在对话框中的子域。 例如，输入格式的域名称`www.contoso.com`或`cdn.contoso.com`。   
   
   Azure 将验证您输入的域名存在 CNAME 记录。 如果 CNAME 正确，请验证你的自定义域。  有关**Verizon 从 Azure CDN** （标准版和高级） 终结点，它可能需要长达 90 分钟的自定义域设置传播到所有 CDN 边缘节点，但是。  
   
   请注意，在某些情况下可能需要时间来将 CNAME 记录传播到 Internet 上的名称服务器。 如果你的域没有立即生效，并且您认为 CNAME 记录是正确，然后等待几分钟，然后重试。

## <a name="register-a-custom-domain-for-an-azure-cdn-endpoint-using-the-intermediary-cdnverify-subdomain"></a>注册自定义域使用中间 cdnverify 子域为 Azure CDN 终结点
1. 登录到[Azure 门户](https://portal.azure.com/)。
2. 单击**浏览**，然后**CDN 配置文件**，则需要将映射到自定义域与终结点的 CDN 配置文件。  
3. 在**的 CDN 配置文件**边栏选项卡，单击你想要将子域关联的 CDN 终结点。
4. 在终结点边栏选项卡的顶部，单击**添加自定义域**按钮。  在**添加自定义域**边栏选项卡，你将看到从 CDN 终结点，以便在创建新的 CNAME 记录使用派生的终结点主机名。 主机名地址的格式将显示为 **&lt;EndpointName >。 azureedge.net**。  你可以复制要在创建 CNAME 记录时使用此主机名。
5. 导航到你的域注册机构的网站，并找到用于创建 DNS 记录的部分。 你可能这部分中找到如**域名**， **DNS**，或**名称服务器管理**。
6. 找到用于管理 Cname 的部分。 你可能需要转至高级的设置页面并查找词**CNAME**，**别名**，或**子域**。
7. 创建一个新的 CNAME 记录，并且提供子域别名，其中包含**cdnverify**子域。 例如，你指定的子域将采用格式**cdnverify.www**或**cdnverify.cdn**。 然后提供主机名，这是你的 CDN 终结点，格式**cdnverify。&lt;EndpointName >。 azureedge.net**。 您的 DNS 映射应如下所示：`cdnverify.www.consoto.com   CNAME   cdnverify.consoto.azureedge.net`  
8. 返回到**添加自定义域**边栏选项卡，然后输入你的自定义域，包括在对话框中的子域。 例如，输入格式的域名称`www.contoso.com`或`cdn.contoso.com`。 请注意，在此步骤中，你不需要以与为子域添加前缀**cdnverify**。  
   
    Azure 将验证您输入的 cdnverify 域名存在 CNAME 记录。
9. 此时，已由 Azure 中，验证你的自定义域，但向您的域的流量将不被路由到你的 CDN 终结点。 在等待足够长的时间以允许自定义域设置传播到 CDN 边缘节点 (90 分钟**Verizon 从 Azure CDN**，1-2 分钟**Akamai 从 Azure CDN**)，返回到 DNS 注册机构的网站，并创建将你的子域映射到 CDN 终结点的另一个 CNAME 记录。 例如，指定为子域**www**或**cdn**，为将主机名 **&lt;EndpointName >。 azureedge.net**。 使用此步骤中，你的自定义域的注册已完成。
10. 最后，你可以删除你创建使用 CNAME 记录**cdnverify**，因为它需要仅作为中间步骤。  

## <a name="verify-that-the-custom-subdomain-references-your-cdn-endpoint"></a>验证自定义子域引用你的 CDN 终结点
* 完成你的自定义域的注册后，你可以在你的 CDN 终结点使用自定义域访问缓存的内容。
  首先，确保你有在终结点缓存的公共内容。 例如，如果你的 CDN 终结点与存储帐户相关联，CDN 缓存公共 blob 容器中的内容。 若要测试自定义域，请确保你的容器设置为允许公共访问权限，并且它包含至少一个 blob。
* 在浏览器中，导航到使用自定义域的 blob 的地址。 例如，如果你的自定义域为`cdn.contoso.com`，缓存的 blob 的 URL 将类似于以下 URL: http://cdn.contoso.com/mypubliccontainer/acachedblob.jpg

## <a name="see-also"></a>另请参阅
[如何对 Azure 启用内容交付网络 (CDN)](cdn-create-new-endpoint.md)  

