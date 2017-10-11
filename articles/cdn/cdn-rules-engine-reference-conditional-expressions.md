---
title: "Azure CDN 规则引擎的条件表达式 |Microsoft 文档"
description: "Azure CDN 的参考文档规则引擎的匹配条件和功能。"
services: cdn
documentationcenter: 
author: Lichard
manager: akucer
editor: 
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: rli
ms.openlocfilehash: 57e56c38e003cb83dcf44f455c4451d159db8a59
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="azure-cdn-rules-engine-conditional-expressions"></a>Azure CDN 规则引擎的条件表达式
本主题列出了有关 Azure 内容交付网络 (CDN) 的条件表达式详细的描述[规则引擎](cdn-rules-engine.md)。

规则的第一部分是条件表达式。

条件表达式 | 描述
-----------------------|-------------
如果 | 如果表达式始终是在规则中的第一个语句的一部分。 像所有其他条件表达式，此 IF 语句必须与匹配相关联。 如果未不定义任何其他条件表达式，此匹配项将确定的一组功能可以应用于请求之前必须满足的条件。
如果 | 仅可能在以下类型的条件表达式： 如果，并且如果后添加和 IF 表达式。 它指示没有必须满足的初始 IF 语句的另一个条件。
ELSE IF| ELSE IF 表达式指定的一组特定于此 ELSE IF 语句的功能发生之前必须满足的可选条件。 ELSE IF 语句的存在指示上一条语句的末尾。 可能的 ELSE IF 语句后放置的仅条件表达式是另一个 ELSE IF 语句。 这意味着，ELSE IF 语句可能仅使用指定必须满足的单个其他条件。

**示例**: ![CDN 与条件匹配](./media/cdn-rules-engine-reference/cdn-rules-engine-conditional-expression.png)

 > [!TIP]
   > 后续规则可能会覆盖以前的规则指定的操作。 示例： 全方位规则保护通过基于令牌的身份验证的所有请求。 另一个规则可能会创建它，以使异常对于某些类型的请求的正下方。

### <a name="next-steps"></a>后续步骤
* [Azure CDN 概述](cdn-overview.md)
* [规则引擎参考](cdn-rules-engine-reference.md)
* [规则引擎的匹配条件](cdn-rules-engine-reference-match-conditions.md)
* [规则引擎功能](cdn-rules-engine-reference-features.md)
* [重写默认 HTTP 行为使用的规则引擎](cdn-rules-engine.md)
