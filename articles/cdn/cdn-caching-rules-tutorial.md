---
title: 教程 - 设置 Azure CDN 缓存规则 | Microsoft Docs
description: 在本教程中，将设置 Azure CDN 全局缓存规则和自定义缓存规则。
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/20/2018
ms.author: v-deasim
ms.custom: mvc
ms.openlocfilehash: a4b5a6a44fe9271f6ff9627c1c5623f0031f23ca
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34608961"
---
# <a name="tutorial-set-azure-cdn-caching-rules"></a>教程：设置 Azure CDN 缓存规则

> [!NOTE] 
> Azure CDN 缓存规则仅适用于 **Verizon 提供的标准 Azure CDN** 和 **Akamai 提供的标准 Azure CDN**。 对于 **Verizon 提供的高级 Azure CDN**，可以在“管理”门户中使用 [Azure CDN 规则引擎](cdn-rules-engine.md)来获得类似的功能。
 

本教程介绍如何使用 Azure 内容分发网络 (CDN) 缓存规则，全局性地和根据自定义条件（例如 URL 路径和文件扩展名）设置或修改默认的缓存过期行为。 Azure CDN 提供两种类型的缓存规则：
- 全局缓存规则：可以针对配置文件中的每个终结点设置一个全局缓存规则，该规则影响对该终结点发出的所有请求。 全局缓存规则会替代所有 HTTP 缓存指令标头（如果已设置）。

- 自定义缓存规则：可以针对配置文件中的每个终结点设置一个或多个自定义缓存规则。 自定义缓存规则将匹配特定的路径和文件扩展名，按顺序进行处理，并替代全局缓存规则（如果已设置）。 

本教程介绍如何执行下列操作：
> [!div class="checklist"]
> - 打开“缓存规则”页。
> - 创建全局缓存规则。
> - 创建自定义缓存规则。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

在完成本教程中的步骤之前，必须先创建一个 CDN 配置文件，一个至少一个 CDN 终结点。 有关详细信息，请参阅[快速入门：创建 Azure CDN 配置文件和终结点](cdn-create-new-endpoint.md)。

## <a name="open-the-azure-cdn-caching-rules-page"></a>打开 Azure CDN“缓存规则”页

1. 在 [Azure 门户](https://portal.azure.com)中，依次选择一个 CDN 配置文件和一个终结点。

2. 在左窗格中的“设置”下，选择“缓存规则”。

   ![CDN 缓存规则按钮](./media/cdn-caching-rules/cdn-caching-rules-btn.png)

   “缓存规则”页随即出现。

   ![CDN“缓存规则”页](./media/cdn-caching-rules/cdn-caching-rules-page.png)


## <a name="set-global-caching-rules"></a>设置全局缓存规则

按如下所述创建一个全局缓存规则：

1. 在“全局缓存规则”下面，将“查询字符串缓存行为”设置为“忽略查询字符串”。

2. 将“缓存行为”设置为“缺少时设置”。
       
3. 对于“缓存过期持续时间”，请在“天”字段中输入 10。

    全局缓存规则会影响对终结点发出的所有请求。 此规则遵循源缓存指令标头（如果存在，值为 `Cache-Control` 或 `Expires`）；如果未指定此类标头，则将缓存设置为 10 天。 

    ![全局缓存规则](./media/cdn-caching-rules/cdn-global-caching-rules.png)

## <a name="set-custom-caching-rules"></a>设置自定义缓存规则

按如下所述创建一个自定义缓存规则：

1. 在“自定义缓存规则”下面，将“匹配条件”设置为“路径”，将“匹配值”设置为 `/images/*.jpg`。
    
2. 将“缓存行为”设置为“替代”，并在“天”字段中输入 30。
       
    此自定义缓存规则针对终结点的 `/images` 文件夹中的所有 `.jpg` 图像文件设置 30 天缓存持续时间。 它会替代源服务器发送的所有 `Cache-Control` 或 `Expires` HTTP 标头。

    ![自定义缓存规则](./media/cdn-caching-rules/cdn-custom-caching-rules.png)

    
## <a name="clean-up-resources"></a>清理资源

在前面的步骤中，已创建了缓存规则。 如果不再想要使用这些缓存规则，则可以通过执行下列步骤来将其删除：
 
1. 选择 CDN 配置文件，然后选择其缓存规则需要删除的终结点。

2. 在左窗格中的“设置”下，选择“缓存规则”。

3. 在“全局缓存规则”下，将“缓存行为”设置为“未设置”。
 
4. 在“自定义缓存规则”下，选中要删除的规则旁边的复选框。

5. 选择“删除”。

6. 从页面顶部，选择“保存”。


## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> - 打开“缓存规则”页。
> - 创建全局缓存规则。
> - 创建自定义缓存规则。

转到下一篇文章，了解如何配置其他缓存规则设置。

> [!div class="nextstepaction"]
> [使用缓存规则控制 Azure CDN 缓存行为](cdn-caching-rules.md)



