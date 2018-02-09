---
title: "Azure CDN 入门 | Microsoft 文档"
description: "本主题介绍如何启用 Azure 内容交付网络 (CDN)。 本教程介绍新 CDN 配置文件和终结点的创建。"
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 4ca51224-5423-419b-98cf-89860ef516d2
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/25/2018
ms.author: mazha
ms.openlocfilehash: 81a88f6495ca9092ca3b55b8ffb3e41def3b4623
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2018
---
# <a name="getting-started-with-azure-cdn"></a>Azure CDN 入门
本文介绍如何通过创建新的 Azure 内容交付网络 (CDN) 配置文件和终结点来启用 CDN。

> [!IMPORTANT]
> 如需 CDN 简介和功能的列表，请参阅 [CDN 概述](cdn-overview.md)。
> 
> 

## <a name="create-a-new-cdn-profile"></a>创建新的 CDN 配置文件
CDN 配置文件是 CDN 终结点的集合。 每个配置文件可以包含一个或多个 CDN 终结点。 若要通过 Internet 域、Web 应用程序或某些其他条件来组织 CDN 终结点，可以使用多个配置文件。

> [!NOTE]
> Azure 订阅对以下资源具有默认限制：
> - 可以创建的 CDN 配置文件数
> - 可以在 CDN 配置文件中创建的终结点的数量 
> - 可以映射到终结点的自定义域的数量
>
> 有关 CDN 订阅限制的信息，请参阅 [CDN 限制](https://docs.microsoft.com/azure/azure-subscription-service-limits#cdn-limits)。
>
> 可在 CDN 配置文件级别应用 CDN 定价。 因此，若要混合使用 Azure CDN 定价层，必须创建多个 CDN 配置文件。
> 
> 

[!INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## <a name="create-a-new-cdn-endpoint"></a>创建新的 CDN 终结点
**创建新的 CDN 终结点**

1. 在 [Azure 门户](https://portal.azure.com)中，导航到 CDN 配置文件。 可能在先前步骤中将其固定到了仪表板。 如果还没有，则可进行查找，方法是：先选择“所有服务”，然后选择“CDN 配置文件”。 在“CDN 配置文件”窗格中，选择打算向其添加终结点的配置文件。 
   
    此时会显示“CDN 配置文件”窗格。
   
    ![CDN 配置文件][cdn-profile-settings]

2. 选择“终结点”。
   
    ![“添加终结点”按钮][cdn-new-endpoint-button]
   
    此时会显示“添加终结点”窗格。
   
    ![“添加终结点”窗格][cdn-add-endpoint]

3. 至于“名称”，请输入新建 CDN 终结点的唯一名称。 此名称用于访问在域 `<endpointname>.azureedge.net` 中缓存的资源。

4. 至于“源服务器类型”，请选择一个源服务器类型。 为 Azure 存储帐户选择“存储”，为 Azure 云服务选择“云服务”，为 Azure Web 应用选择“Web 应用”，为其他任何公开可用 Web 服务器原点（托管在 Azure 或其他位置）选择“自定义原点”。
   
    ![CDN 原点类型](./media/cdn-create-new-endpoint/cdn-origin-type.png)

5. 至于“源服务器主机名”，请选择或输入原始域。 下拉列表列出其类型已在步骤 4 中指定的所有可用的源服务器。 如果选择“自定义源服务器”作为源服务器类型，请输入自定义源服务器的域。
    
6. 至于“源服务器路径”，请输入要缓存的资源的路径，或将其留空，以便缓存在步骤 5 中指定的域的任何资源。
    
7. 至于“源服务器主机头”，请输入需要让 Azure CDN 随每个请求发送的主机头，或保留默认值。
   
   > [!WARNING]
   > 某些类型的原点（例如 Azure 存储和 Web 应用）需要主机头来匹配原点的域。 除非拥有的原点要求不同于其域的主机头，否则应当保留默认值。
   > 
    
8. 对于**协议**和**原点端口**，指定用于访问原点中资源的协议和端口。 必须至少选择一种协议（HTTP 或 HTTPS）。 使用 CDN 提供的域 (`<endpointname>.azureedge.net`) 来访问 HTTPS 内容。 
   
   > [!NOTE]
   > “源服务器端口”值只决定终结点用来从源服务器检索信息的端口。 终结点自身仅可用于默认 HTTP 和 HTTPS 端口（80 和 443）上的最终客户端，不管“源服务器端口”值如何。  
   > 
   > **来自 Akamai 的 Azure CDN** 配置文件中的终结点不允许适用于源服务器端口的完整 TCP 端口范围。 有关不被允许的原点端口列表，请参阅 [来自 Akamai 的 Azure CDN 受允许原点端口](https://msdn.microsoft.com/library/mt757337.aspx)。  
   > 
   > 使用 HTTPS 访问 CDN 内容时，存在以下约束：
   > 
   > * 使用 CDN 提供的 SSL 证书。 不支持第三方证书。
   > * HTTPS 对 Azure CDN 自定义域的支持仅在 **Verizon 的 Azure CDN** 产品（标准版和高级）中可用。 在 **Akamai 的 Azure CDN** 产品上不受支持。 有关详细信息，请参阅[在 Azure CDN 自定义域上配置 HTTPS](cdn-custom-ssl.md)。
    
9. 选择“添加”可创建新的终结点。
   
   终结点在创建后出现在配置文件的终结点列表中。
    
   ![CDN 终结点][cdn-endpoint-success]
    
   > [!IMPORTANT]
   > 终结点不会立即可供使用，因为注册传播需花时间。 对于**来自 Akamai 的 Azure CDN** 配置文件，传播通常可在一分钟内完成。 对于**来自 Verizon 的 Azure CDN** 配置文件，传播通常在 90 分钟内完成，但某些情况下可能更长。
    > 
    > 如果尝试在终结点配置传播到 POP 之前使用 CDN 域名，则可能会收到 HTTP 404 响应状态。 如果在创建终结点数小时后依然收到 404 响应状态，请参阅[排查 CDN 终结点返回 404 状态的问题](cdn-troubleshoot-endpoint.md)。
    > 
    > 

## <a name="see-also"></a>另请参阅
* [控制含查询字符串请求的缓存行为](cdn-query-string.md)
* [如何将 CDN 内容映射到自定义域](cdn-map-content-to-custom-domain.md)
* [在 Azure CDN 终结点上预加载资产](cdn-preload-endpoint.md)
* [清除 Azure CDN 终结点](cdn-purge-endpoint.md)
* [CDN 终结点返回 404 状态疑难解答](cdn-troubleshoot-endpoint.md)

[cdn-profile-settings]: ./media/cdn-create-new-endpoint/cdn-profile-settings.png
[cdn-new-endpoint-button]: ./media/cdn-create-new-endpoint/cdn-new-endpoint-button.png
[cdn-add-endpoint]: ./media/cdn-create-new-endpoint/cdn-add-endpoint.png
[cdn-endpoint-success]: ./media/cdn-create-new-endpoint/cdn-endpoint-success.png
