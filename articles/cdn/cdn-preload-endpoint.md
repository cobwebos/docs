---
title: 在 Azure CDN 终结点上预加载资产 | Microsoft Docs
description: 了解如何在 Azure CDN 终结点上预加载缓存内容。
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: 5ea3eba5-1335-413e-9af3-3918ce608a83
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2018
ms.author: mazha
ms.openlocfilehash: bf3161d756759e4b278e48ad7a49615e4a73d17f
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
ms.locfileid: "32150128"
---
# <a name="pre-load-assets-on-an-azure-cdn-endpoint"></a>在 Azure CDN 终结点上预加载资产
[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

默认情况下，仅当请求资产时，才会缓存资产。 每个区域中的第一个请求可能比后续请求需要较长时间，因为边缘服务器尚未缓存内容，并且需要将请求转发到源服务器。 若要避免这种第一个命中延迟，请预加载资产。 除了提供更好的客户体验，预加载缓存的资产还可以减少源服务器上的网络流量。

> [!NOTE]
> 预加载资产对于大型事件非常有用，对于要同时提供给许多用户的内容（如新影片发布或软件更新）也很有用。
> 
> 

本教程会逐步指导在所有 Azure CDN 边缘节点上预加载缓存的内容。

## <a name="to-pre-load-assets"></a>预加载资产
1. 在 [Azure 门户](https://portal.azure.com)中，浏览到包含要预加载的终结点的 CDN 配置文件。 此时将打开配置文件窗格。
    
2. 单击列表中的终结点。 此时将打开终结点窗格。
3. 从 CDN 终结点的窗格中，选择“加载”。
   
    ![CDN 终结点窗格](./media/cdn-preload-endpoint/cdn-endpoint-blade.png)
   
    此时将打开“加载”窗格。
   
    ![CDN 加载窗格](./media/cdn-preload-endpoint/cdn-load-blade.png)
4. 对于“内容路径”，输入想要加载的每个资产的完整路径（例如，`/pictures/kitten.png`）。
   
   > [!TIP]
   > 开始输入文本后，会出现更多“内容路径”文本框，以允许生成多个资产的列表。 若要从列表中删除资产，请选择省略号 (...) 按钮，然后选择“删除”。
   > 
   > 每个内容路径必须是符合以下[正则表达式](https://msdn.microsoft.com/library/az24scfc.aspx)的相对 URL：  
   > - 加载单个文件路径：`^(?:\/[a-zA-Z0-9-_.%=\u0020]+)+$`  
   > - 加载包含查询字符串的单个文件：`^(?:\?[-_a-zA-Z0-9\/%:;=!,.\+'&\u0020]*)?$` 
   > 
   > 由于每个资产都必须有其自己的路径，因此预加载资产没有通配符功能。
   > 
   > 
   
    ![“加载”按钮](./media/cdn-preload-endpoint/cdn-load-paths.png)
5. 完成输入内容路径后，请选择“加载”。
   

> [!NOTE]
> 有每个 CDN 配置文件每分钟 10 个加载请求的限制，系统一次可以处理 50 个并发路径。 每个路径的路径长度限制均为 1024 个字符。
> 
> 

## <a name="see-also"></a>另请参阅
* [清除 Azure CDN 终结点](cdn-purge-endpoint.md)
* [Azure CDN REST API 参考：预加载终结点上的内容](https://docs.microsoft.com/rest/api/cdn/endpoints/loadcontent)
* [Azure CDN REST API 参考：清除终结点上的内容](https://docs.microsoft.com/rest/api/cdn/endpoints/purgecontent)

