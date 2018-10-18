---
title: 创建 Azure CDN 终结点 | Microsoft Docs
description: 本文介绍如何创建新的 Azure 内容分发网络 (CDN) 终结点（包括高级设置）。
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: a3fd85d1dbcba769f47e537e0231f63110752492
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2018
ms.locfileid: "49093674"
---
# <a name="create-an-azure-cdn-endpoint"></a>创建 Azure CDN 终结点
本文介绍现有 CDN 配置文件中用于创建 [Azure 内容分发网络 (CDN)](cdn-overview.md) 终结点的所有设置。 创建配置文件和终结点以后，即可将内容交付给客户。 有关如何创建配置文件和终结点的快速入门，请参阅[快速入门：创建 Azure CDN 配置文件和终结点](cdn-create-new-endpoint.md)。

## <a name="prerequisites"></a>先决条件
在创建 CDN 终结点之前，必须已创建至少一个 CDN 配置文件，其中可能包含一个或多个 CDN 终结点。 若要通过 Internet 域、Web 应用程序或某些其他条件来组织 CDN 终结点，可以使用多个配置文件。 由于 CDN 定价是在 CDN 配置文件级别应用的，因此如果需要使用混合的 Azure CDN 定价层，则必须创建多个 CDN 配置文件。 若要创建 CDN 配置文件，请参阅[创建新的 CDN 配置文件](cdn-create-new-endpoint.md#create-a-new-cdn-profile)。

## <a name="log-in-to-the-azure-portal"></a>登录到 Azure 门户
使用 Azure 帐户登录到 [Azure 门户](https://portal.azure.com)。

## <a name="create-a-new-cdn-endpoint"></a>创建新的 CDN 终结点

1. 在 [Azure 门户](https://portal.azure.com)中，导航到 CDN 配置文件。 可能在先前步骤中将其固定到了仪表板。 如果还没有，则可进行查找，方法是：先选择“所有服务”，然后选择“CDN 配置文件”。 在“CDN 配置文件”窗格中，选择打算向其添加终结点的配置文件。 
   
    此时会显示“CDN 配置文件”窗格。

2. 选择“终结点”。
   
    ![CDN 选定终结点](./media/cdn-create-endpoint-how-to/cdn-select-endpoint.png)
   
    此时会显示“添加终结点”页。
   
    ![“添加终结点”页](./media/cdn-create-endpoint-how-to/cdn-add-endpoint-page.png)

3. 至于“名称”，请输入新建 CDN 终结点的唯一名称。 此名称用于访问在 _<endpointname>_.azureedge.net 域中缓存的资源。

4. 对于“源类型”，请选择下述源类型之一： 
   - **存储**：适用于 Azure 存储
   - **云服务**：适用于 Azure 云服务
   - **Web 应用**：适用于 Azure Web 应用
   - **自定义源**：适用于任何其他的可公开访问的源 Web 服务器（托管在 Azure 中或其他位置）

5. 至于“源服务器主机名”，请选择或输入源服务器域。 下拉列表列出其类型已在步骤 4 中指定的所有可用的源服务器。 如果选择“自定义源服务器”作为源服务器类型，请输入自定义源服务器的域。
    
6. 至于“源路径”，请输入要缓存的资源的路径。 若要缓存在步骤 5 中指定的域的任何资源，请将此设置留空。
    
7. 至于“源服务器主机头”，请输入需要让 Azure CDN 随每个请求发送的主机头，或保留默认值。
   
   > [!NOTE]
   > 某些类型的原点（例如 Azure 存储和 Web 应用）需要主机头来匹配原点的域。 除非拥有的原点要求不同于其域的主机头，否则应当保留默认值。
   > 
    
8. 至于“协议”和“源服务器端口”，请指定用于访问源服务器中资源的协议和端口。 必须至少选择一种协议（HTTP 或 HTTPS）。 使用 CDN 提供的域 (_<endpointname>_.azureedge.net) 来访问 HTTPS 内容。 
   
   > [!NOTE]
   > “源服务器端口”值只决定终结点用来从源服务器检索信息的端口。 终结点自身仅可用于默认 HTTP 和 HTTPS 端口（80 和 443）上的最终客户端，不管“源服务器端口”值如何。  
   > 
   > **来自 Akamai 的 Azure CDN** 配置文件中的终结点不允许适用于源服务器端口的完整 TCP 端口范围。 有关不被允许的原点端口列表，请参阅 [来自 Akamai 的 Azure CDN 受允许原点端口](https://msdn.microsoft.com/library/mt757337.aspx)。  
   > 
   > Azure CDN 自定义域的 HTTPS 支持在 **Akamai 的 Azure CDN** 产品上不受支持。 有关详细信息，请参阅[在 Azure CDN 自定义域上配置 HTTPS](cdn-custom-ssl.md)。
    
9. 对于“优化对象”，请选择与方案和要通过终结点传送的内容类型最匹配的优化类型。 有关详细信息，请参阅[针对内容分发类型优化 Azure CDN](cdn-optimization-overview.md)。

    将根据配置文件类型为以下优化类型设置提供支持：
    - **Microsoft 的 Azure CDN 标准版**配置文件：
       - [**常规 Web 分发**](cdn-optimization-overview.md#general-web-delivery)

    - **Verizon 的 Azure CDN 标准版**和 **Verizon 的 Azure CDN 高级版**配置文件：
       - [**常规 Web 分发**](cdn-optimization-overview.md#general-web-delivery)
       - [**动态站点加速**](cdn-optimization-overview.md#dynamic-site-acceleration)

    - **Akamai 的 Azure CDN 标准版**配置文件：
       - [**常规 Web 分发**](cdn-optimization-overview.md#general-web-delivery)
       - [**常规媒体流式处理**](cdn-optimization-overview.md#general-media-streaming)
       - [**点播视频媒体流式处理**](cdn-optimization-overview.md#video-on-demand-media-streaming)
       - [**大文件下载**](cdn-optimization-overview.md#large-file-download)
       - [**动态站点加速**](cdn-optimization-overview.md#dynamic-site-acceleration)

10. 选择“添加”可创建新的终结点。
   
    终结点在创建后出现在配置文件的终结点列表中。
    
    ![CDN 终结点](./media/cdn-create-new-endpoint/cdn-endpoint-success.png)
    
    终结点不会立即可供使用，因为注册传播需花时间： 
    - 对于 **Microsoft 推出的 Azure CDN 标准版**配置文件，传播通常可在 10 分钟内完成。 
    - 对于 **Akamai 的 Azure CDN 标准版**配置文件，传播通常可在一分钟内完成。 
    - 对于 Verizon 的 Azure CDN 标准版和 Verizon 的 Azure CDN 高级版配置文件，传播通常可在 90 分钟内完成。 
   
    如果尝试在终结点配置传播到接入点 (POP) 服务器之前使用 CDN 域名，则可能会收到 HTTP 404 响应状态。 如果在创建终结点数小时后依然收到 404 响应状态，请参阅[排查 Azure CDN 终结点返回 404 状态代码的问题](cdn-troubleshoot-endpoint.md)。

## <a name="clean-up-resources"></a>清理资源
若要删除不再需要的终结点，请将其选中，然后选择“删除”。 

## <a name="next-steps"></a>后续步骤
若要了解自定义域，请继续学习有关如何向 CDN 终结点添加自定义域的教程。

> [!div class="nextstepaction"]
> [添加自定义域](cdn-map-content-to-custom-domain.md)


