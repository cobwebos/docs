---
title: 使用规则引擎在标准 Azure CDN 中强制使用 HTTPS | Microsoft Docs
description: 对 Microsoft 标准 Azure 内容分发网络 (Azure CDN) 使用规则引擎，以自定义 Azure CDN 处理 HTTP 请求的方式，包括阻止分发某些类型的内容、定义缓存策略以及修改 HTTP 标头。 本文介绍如何创建将用户重定向到 HTTPS 的规则。
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: how-to
ms.date: 11/01/2019
ms.author: allensu
ms.openlocfilehash: 1c596bf10d8afbce504b5abf04eacb877989521a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "84887535"
---
# <a name="set-up-the-standard-rules-engine-for-azure-cdn"></a>为 Azure CDN 设置标准规则引擎

本文介绍如何为 Azure 内容分发网络 (Azure CDN) 设置和使用标准规则引擎。

## <a name="standard-rules-engine"></a>标准规则引擎

可以对 Azure CDN 使用标准规则引擎，以自定义处理 HTTP 请求的方式。 例如，可以使用规则引擎来强制内容分发使用特定协议、定义缓存策略或修改 HTTP 标头。 本文演示如何创建自动将用户重定向到 HTTPS 的规则。 

> [!NOTE]
> 本文中所述的规则引擎仅适用于 Microsoft 的标准 Azure CDN。 

## <a name="redirect-users-to-https"></a>将用户重定向到 HTTPS

1. 在 Microsoft 配置文件中，请转到 Azure 内容分发网络。

1. 在“CDN 配置文件”页中，选择要为其创建规则的终结点。
  
1. 选择“规则引擎”选项卡。
   
    “规则引擎”窗格随即打开，并显示可用全局规则的列表。 
   
    [![Azure CDN 新建规则页](./media/cdn-standard-rules-engine/cdn-new-rule.png)](./media/cdn-standard-rules-engine/cdn-new-rule.png#lightbox)
   
   > [!IMPORTANT]
   > 多个规则的列出顺序会影响处理规则的方式。 规则中指定的操作可能会被后续规则覆盖。
   >

1. 选择“添加规则”并输入规则名称。 规则名称必须以字母开头，只能包含字母和数字。

1. 若有标识规则适用的请求类型，请创建匹配条件：
    1. 选择“添加条件”，然后选择“请求协议”匹配条件。
    1. 对于“运算符”，选择“等于” 。
    1. 对于“值”，选择“HTTP” 。
   
   [![Azure CDN 规则匹配条件](./media/cdn-standard-rules-engine/cdn-match-condition.png)](./media/cdn-standard-rules-engine/cdn-match-condition.png#lightbox)
   
   > [!NOTE]
   > 可以从“添加条件”下拉列表中的多个匹配条件中进行选择。 有关匹配条件的详细列表，请参阅[标准规则引擎中的匹配条件](cdn-standard-rules-engine-match-conditions.md)。
   
1. 选择要应用于满足匹配条件的请求的操作：
   1. 选择“添加操作”，然后选择“URL 重定向”。
   1. 对于“类型”，请选择“找到(302)”。 
   1. 选择 **HTTPS** 作为“协议”。
   1. 将其他所有字段保留为空以使用传入值。
   
   [![Azure CDN 规则操作](./media/cdn-standard-rules-engine/cdn-action.png)](./media/cdn-standard-rules-engine/cdn-action.png#lightbox)
   
   > [!NOTE]
   > 可以从“添加操作”下拉列表中的多个操作中进行选择。 有关操作的详细列表，请参阅[标准规则引擎中的操作](cdn-standard-rules-engine-actions.md)。

6. 选择“保存”，以保存新规则。 现在可使用规则。
   
   > [!IMPORTANT]
   > 规则更改最多可能需要 15 分钟，才能在整个 Azure CDN 中传播。
   >
   

## <a name="next-steps"></a>后续步骤

- [Azure CDN 概述](cdn-overview.md)
- [标准规则引擎参考](cdn-standard-rules-engine-reference.md)
- [标准规则引擎中的匹配条件](cdn-standard-rules-engine-match-conditions.md)
- [标准规则引擎中的操作](cdn-standard-rules-engine-actions.md)
