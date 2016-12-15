---
title: "在 Azure CDN 终结点上预加载资产 | Microsoft Docs"
description: "了解如何在 CDN 终结点上预加载缓存的内容。"
services: cdn
documentationcenter: 
author: camsoper
manager: erikre
editor: 
ms.assetid: 5ea3eba5-1335-413e-9af3-3918ce608a83
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/28/2016
ms.author: casoper
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e54ab1d1e1d363f92c05772a5367b28c46fe4038


---
# <a name="pre-load-assets-on-an-azure-cdn-endpoint"></a>在 Azure CDN 终结点上预加载资产
[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

默认情况下，资产在收到请求时会首先缓存。 这意味着每个区域中的第一个请求可能需要较长时间，因为边缘服务器没有缓存内容，并且需要将请求转发到源服务器。 预加载内容以避免第一个命中的滞后时间。

除了提供更好的客户体验，预加载缓存的资产还可以减少源服务器上的网络流量。

> [!NOTE]
> 预加载资产对于大型事件非常有用，对于要同时提供给大量用户的内容（如新影片发行或软件更新）也很有用。
> 
> 

本教程将逐步指导你在所有 Azure CDN 边缘节点上预加载缓存的内容。

## <a name="walkthrough"></a>演练
1. 在 [Azure 门户](https://portal.azure.com)中，浏览到包含你想要预加载的终结点的 CDN 配置文件。  配置文件边栏选项卡随即打开。
2. 单击列表中的终结点。  终结点边栏选项卡打开。
3. 从 CDN 终结点的边栏选项卡，单击“加载”按钮。
   
    ![CDN 终结点边栏选项卡](./media/cdn-preload-endpoint/cdn-endpoint-blade.png)
   
    “加载”边栏选项卡随即打开。
   
    ![CDN 加载边栏选项卡](./media/cdn-preload-endpoint/cdn-load-blade.png)
4. 在“**路径**”文本框中输入你想要加载的每个资产的完整路径 (例如，`/pictures/kitten.png`。
   
   > [!TIP]
   > 输入文本后，会显示更多“**路径**”文本框，以便允许你生成包含多个资产的列表。  通过单击省略号 (...) 按钮，可以从列表中删除资产。
   > 
   > 路径必须是符合以下[正则表达式](https://msdn.microsoft.com/library/az24scfc.aspx)的相对 URL：`^(?:\/[a-zA-Z0-9-_.\u0020]+)+$`。  每个资产都必须有其自己的路径。  预加载的资产没有通配符功能。
   > 
   > 
   
    ![“加载”按钮](./media/cdn-preload-endpoint/cdn-load-paths.png)
5. 单击“**加载**”按钮。
   
    ![“加载”按钮](./media/cdn-preload-endpoint/cdn-load-button.png)

> [!NOTE]
> 每个 CDN 配置文件都有每分钟 10 个加载请求的限制。
> 
> 

## <a name="see-also"></a>另请参阅
* [清除 Azure CDN 终结点](cdn-purge-endpoint.md)
* [Azure CDN REST API 参考 - 清除或预加载终结点](https://msdn.microsoft.com/library/mt634451.aspx)




<!--HONumber=Nov16_HO3-->


