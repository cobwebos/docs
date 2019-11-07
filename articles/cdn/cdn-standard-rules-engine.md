---
title: 使用 Azure CDN 标准规则引擎强制执行 HTTPS |Microsoft Docs
description: 标准规则引擎允许你自定义从 Microsoft Azure CDN 如何处理 HTTP 请求，例如阻止传递某些类型的内容、定义缓存策略以及修改 HTTP 标头。
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: b24c4a04e0c02258a918ee075066d90c22ea0c75
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615918"
---
# <a name="azure-cdn-standard-rules-engine"></a>Azure CDN 标准规则引擎

> [!NOTE]
> 标准规则引擎仅适用于 Microsoft Azure CDN。 

Azure CDN 标准规则引擎允许你自定义处理 HTTP 请求的方式。 例如，通过特定协议强制执行内容传递、定义缓存策略或修改 HTTP 标头。 本教程演示如何创建一个将用户自动重定向到 HTTPS 的规则。 


## <a name="tutorial"></a>教程

1. 从 Microsoft 配置文件 Azure CDN 上的 " **CDN 配置文件**" 页中，选择要为其配置规则的终结点。
  
2. 选择左侧的 "**规则引擎**" 选项卡。
   
    将显示 "规则引擎" 边栏选项卡，其中包含全局规则。 
   
    [![CDN 新建规则 "页](./media/cdn-standard-rules-engine/cdn-new-rule.png)](./media/cdn-standard-rules-engine/cdn-new-rule.png#lightbox)
   
   > [!IMPORTANT]
   > 多个规则的列出顺序会影响处理它们的方式。 后一个规则可能会覆盖前一个规则所指定的操作。
   >

3. 单击 "**添加规则**" 按钮，并提供规则名称。 规则名称必须以字母开头，并且仅包含数字和字母。

4. 标识该规则适用的请求类型。 使用下拉选择 "**请求协议**匹配条件"，并使用 "值**等于** **HTTP**"。
   
   [![CDN 规则匹配条件](./media/cdn-standard-rules-engine/cdn-match-condition.png)](./media/cdn-standard-rules-engine/cdn-match-condition.png#lightbox)
   
   > [!NOTE]
   > 此下拉列表中提供了多个匹配条件。 有关匹配条件的详细列表，请参阅[规则引擎匹配条件](cdn-standard-rules-engine-match-conditions.md)。
   
5. 选择将应用于标识的请求的操作。 使用下拉列表选择 " **URL 重定向**" 操作，并为 "类型" 和 "协议的**HTTPS** " 使用找到的值 **（302）** 。 将其他所有字段保留为空以使用传入值。
   
   [![CDN 规则操作](./media/cdn-standard-rules-engine/cdn-action.png)](./media/cdn-standard-rules-engine/cdn-action.png#lightbox)
   
   > [!NOTE]
   > 下拉列表中有多个操作。 有关操作的详细列表，请参阅[规则引擎操作](cdn-standard-rules-engine-actions.md)。

6. 选择 "**保存**" 以保存新规则。  此时将部署新规则。
   
   > [!IMPORTANT]
   > 规则更改最长可能需要15分钟才能传播到 Azure CDN。
   >
   

## <a name="see-also"></a>另请参阅

- [Azure CDN 概述](cdn-overview.md)
- [标准规则引擎参考](cdn-standard-rules-engine-reference.md)
- [标准规则引擎匹配条件](cdn-standard-rules-engine-match-conditions.md)
- [标准规则引擎操作](cdn-standard-rules-engine-actions.md)
