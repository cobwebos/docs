---
title: 来自 Verizon 高级版的 azure CDN 规则引擎条件表达式 |Microsoft Docs
description: 参考文档，了解 Azure CDN from Verizon Premium 规则引擎匹配条件和功能。
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: f790e37ae876c0640d55ebfb51abb43c6a705f04
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593218"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-conditional-expressions"></a>来自 Verizon 的高级规则引擎条件表达式的 azure CDN

本文列出了有关 Azure 内容交付网络 (CDN) 的条件表达式的详细的描述[规则引擎](cdn-verizon-premium-rules-engine.md)。

规则的第一部分是条件表达式。

条件表达式 | 描述
-----------------------|-------------
IF | IF 表达式始终是规则中第一个声明的一部分。 与其他所有条件表达式一样，这个 IF 声明必须与匹配项关联。 如果不定义了任何其他条件表达式，此匹配项确定的一组功能可能会应用到一个请求之前必须满足的条件。
AND IF | AND IF 表达式仅可添加在以下类型的条件表达式之后：IF、AND IF。 它指示初始 IF 语句必须满足其他条件。
ELSE IF| ELSE IF 表达式指定在一组特定于此 ELSE IF 语句的功能发生之前必须满足的替代条件。 存在 ELSE IF 语句即表示之前语句已结束。 可放在 ELSE IF 语句之后的唯一条件表达式是其他 ELSE IF 语句。 这意味着 ELSE IF 语句仅可用于指定必须满足的单个附加条件。

**示例**：![CDN 匹配条件](./media/cdn-rules-engine-reference/cdn-rules-engine-conditional-expression.png)

 > [!TIP]
   > 后一个规则可能会覆盖前一个规则所指定的操作。
   > 例如：全方位的规则保护通过基于令牌的身份验证的所有请求。 在它下面可能会创建其他规则，以便对某种请求做例外处理。

## <a name="next-steps"></a>后续步骤

- [Azure CDN 概述](cdn-overview.md)
- [规则引擎参考](cdn-verizon-premium-rules-engine-reference.md)
- [规则引擎匹配条件](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [规则引擎功能](cdn-verizon-premium-rules-engine-reference-features.md)
- [使用规则引擎重写默认 HTTP 行为](cdn-verizon-premium-rules-engine.md)