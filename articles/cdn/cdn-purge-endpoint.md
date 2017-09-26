---
title: "清除 Azure CDN 终结点 | Microsoft Docs"
description: "了解如何从 Azure CDN 终结点清除所有缓存的内容。"
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 0b50230b-fe82-4740-90aa-95d4dde8bd4f
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.translationtype: Human Translation
ms.sourcegitcommit: eb46368541551feff1b28642f9343e508ef47b95
ms.openlocfilehash: b035c232bb58d653960190d4974cc3789d55a51d
ms.contentlocale: zh-cn
ms.lasthandoff: 02/14/2017

---
# <a name="purge-an-azure-cdn-endpoint"></a>清除 Azure CDN 终结点
## <a name="overview"></a>概述
直到资产的生存时间 (TTL) 到期之前，Azure CDN 边缘节点会将缓存资产。  资产的 TTL 到期后，当客户端从边缘节点请求资产时，边缘节点将检索具有最新更新的资产副本，以满足客户端请求并存储刷新缓存。

确保用户始终获取资产的最新副本的最佳做法是针对每次更新将资产版本化，并将其发布为新 URL。  CDN 会立即检索下用于一个客户端请求的新资产。  有时候你可能希望从所有边缘节点清除缓存的内容，并强制其全部检索新的已更新的资产。  这可能是由于 Web 应用程序获得了更新，或快速更新的资产包含不正确的信息。

> [!TIP]
> 请注意，清除仅限于清除 CDN 边缘服务器上缓存的内容。  任何下游缓存，如代理服务器和本地浏览器缓存，仍会保留该文件的缓存副本。  设置生存时间时请务必记住这一点。  可以在每次更新文件时为其提供一个唯一的名称，或利用[查询字符串缓存](cdn-query-string.md)来强制让下游客户端请求文件的最新版本。  
> 
> 

本教程将逐步指导你完成从某个终结点的所有边缘节点清除资产。

## <a name="walkthrough"></a>演练
1. 在 [Azure 门户](https://portal.azure.com)中，浏览到包含你想要清除的终结点的 CDN 配置文件。
2. 从 CDN 配置文件的边栏选项卡，单击“清除”按钮。
   
    ![CDN 配置文件边栏选项卡](./media/cdn-purge-endpoint/cdn-profile-blade.png)
   
    “清除”边栏选项卡打开。
   
    ![CDN 清除边栏选项卡](./media/cdn-purge-endpoint/cdn-purge-blade.png)
3. 在“清除”边栏选项卡上，从 URL 下拉列表中选择你想要清除的服务地址。
   
    ![清除窗体](./media/cdn-purge-endpoint/cdn-purge-form.png)
   
   > [!NOTE]
   > 也可以通过单击 CDN 终结点边栏选项卡上的**清除**按钮进入“清除”边栏选项卡。  在这种情况下，**URL** 字段将预填充该特定终结点的服务地址。
   > 
   > 
4. 选择你想要从边缘节点清除的资产。  如果想清除所有资产，单击“**全部清除**”复选框。  否则，请在“路径”文本框中输入要清除的每个资产的路径。 路径支持以下格式。
    1. **单个 URL 清除**：通过指定完整 URL 清除单个资产（包含或不包含文件扩展名，例如 `/pictures/strasbourg.png`；`/pictures/strasbourg`）
    2. **通配符清除**：星号 (\*) 可用作通配符。 清除路径中含 `/*` 的终结点下的所有文件夹、子文件夹和文件，或通过指定后跟 `/*`（例如 `/pictures/*`）的文件夹来清除特定文件夹下的所有子文件夹和文件。  请注意，目前来自 Akamai 的 Azure CDN 不支持通配符清除。 
    3. **根域清除**：清除路径中具有“/”的终结点的根。
   
   > [!TIP]
   > 必须指定用于清除的路径，且路径必须是符合以下[正则表达式](https://msdn.microsoft.com/library/az24scfc.aspx)的相对 URL。 目前**来自 Akamai 的 Azure CDN** 不支持**清除所有**和**通配符清除**。
   > > 单个 URL 清除 `@"^\/(?>(?:[a-zA-Z0-9-_.%=\(\)\u0020]+\/?)*)$";`  
   > > 查询字符串 `@"^(?:\?[-\@_a-zA-Z0-9\/%:;=!,.\+'&\(\)\u0020]*)?$";`  
   > > 通配符清除 `@"^\/(?:[a-zA-Z0-9-_.%=\(\)\u0020]+\/)*\*$";`。 
   > 
   > 输入文本后，会显示更多“**路径**”文本框，以便允许你生成包含多个资产的列表。  通过单击省略号 (...) 按钮，可以从列表中删除资产。
   > 
5. 单击**清除**按钮。
   
    ![“清除”按钮](./media/cdn-purge-endpoint/cdn-purge-button.png)

> [!IMPORTANT]
> 对于**来自 Verizon 的 Azure CDN**（标准和高级），清除请求需要 2-3 分钟的时间来处理；对于**来自 Akamai 的 Azure CDN**，大约需要 7 分钟。  在任何给定时间，Azure CDN 都有最多 50 个并发清除请求的限制。 
> 
> 

## <a name="see-also"></a>另请参阅
* [在 Azure CDN 终结点上预加载资产](cdn-preload-endpoint.md)
* [Azure CDN REST API 参考 - 清除或预加载终结点](https://msdn.microsoft.com/library/mt634451.aspx)


