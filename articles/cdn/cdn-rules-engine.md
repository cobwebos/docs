---
title: "使用 Azure CDN 规则引擎重写 HTTP 行为 | Microsoft Docs"
description: "规则引擎允许自定义 Azure CDN 如何处理 HTTP 请求，例如，阻止交付某些类型的内容、定义缓存策略以及修改 HTTP 标头。"
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 625a912b-91f2-485d-8991-128cc194ee71
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: abfe283476206b181018d187675b47112dc5ad2f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="override-http-behavior-using-the-azure-cdn-rules-engine"></a>使用 Azure CDN 规则引擎重写 HTTP 行为
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>概述
使用规则引擎，可允许自定义 HTTP 请求的处理方式，例如，阻止交付某些类型的内容、定义缓存策略以及修改 HTTP 标头。  本教程会演示如何创建一个用于更改 CDN 资产的缓存行为的规则。  “[另请参阅](#see-also)”一节还提供了视频内容。

   > [!TIP] 
   > 有关语法引用的详细信息，请参阅[规则引擎参考](cdn-rules-engine-reference.md)。
   > 


## <a name="tutorial"></a>教程
1. 从 CDN 配置文件的边栏选项卡，单击“**管理**”按钮。
   
    ![CDN 配置文件边栏选项卡管理按钮](./media/cdn-rules-engine/cdn-manage-btn.png)
   
    随即 CDN 管理门户打开。
2. 单击“**HTTP 大型**”选项卡，并单击“**规则引擎**”。
   
    将显示新规则的选项。
   
    ![CDN 新规则选项](./media/cdn-rules-engine/cdn-new-rule.png)
   
   > [!IMPORTANT]
   > 多个规则的列出顺序会影响处理它们的方式。 后一个规则可能会覆盖前一个规则所指定的操作。
   > 
   > 
3. 在“**名称/描述**”文本框中输入一个名称。
4. 标识该规则将适用的请求类型。  默认情况下，选择“**始终**”匹配条件。  在本教程中将使用“**始终**”条件，因此请保留该选择。
   
   ![CDN 匹配条件](./media/cdn-rules-engine/cdn-request-type.png)
   
   > [!TIP]
   > 下拉列表中提供了很多类型的匹配条件。  单击匹配条件左侧的蓝色信息图标，会显示当前所选条件的详细信息。
   > 
   >  有关条件表达式完整列表的详细信息，请参阅[规则引擎条件表达式](cdn-rules-engine-reference-match-conditions.md)。
   >  
   > 有关匹配条件完整列表的详细信息，请参阅[规则引擎匹配条件](cdn-rules-engine-reference-match-conditions.md)。
   > 
   > 
5. 单击“**功能**”旁边的 **+** 按钮，添加一项新功能。  在左侧的下拉列表中，选择“**强制内部最长时间**”。  在显示的文本框中，输入 **300**。  保留其余默认值。
   
   ![CDN 功能](./media/cdn-rules-engine/cdn-new-feature.png)
   
   > [!NOTE]
   > 跟匹配条件一样，单击新功能左侧的蓝色信息图标，会显示有关此功能的详细信息。  在使用“**强制内部最长时间**”的情况下，我们将重写该资产的 **Cache-Control** 和 **Expires** 标头，以控制 CDN 边缘节点何时刷新来自源的资产。  此示例为 300 秒，意味着在刷新来自其源的资产之前，CDN 边缘节点将缓存该资产 5 分钟。
   > 
   > 有关功能完整列表的详细信息，请参阅[规则引擎功能详细信息](cdn-rules-engine-reference-features.md)。
   > 
   > 
6. 单击“**添加**”按钮保存新规则。  新规则现在正在等待批准。 一旦获得批准，状态将从“**挂起 XML**”更改为“**活动 XML**”。
   
   > [!IMPORTANT]
   > 规则的更改可能需要 90 分钟的时间才能传播整个 CDN。
   > 
   > 

## <a name="see-also"></a>另请参阅
* [Azure CDN 概述](cdn-overview.md)
* [规则引擎参考](cdn-rules-engine-reference.md)
* [规则引擎匹配条件](cdn-rules-engine-reference-match-conditions.md)
* [规则引擎条件表达式](cdn-rules-engine-reference-conditional-expressions.md)
* [规则引擎功能](cdn-rules-engine-reference-features.md)
* [使用规则引擎重写默认 HTTP 行为](cdn-rules-engine.md)
* [Azure Fridays: Azure CDN's powerful new Premium Features](https://azure.microsoft.com/documentation/videos/azure-cdns-powerful-new-premium-features/)（Azure 星期五：Azure CDN 强大的新高级功能）（视频）