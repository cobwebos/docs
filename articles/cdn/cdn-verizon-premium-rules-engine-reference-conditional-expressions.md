---
title: Azure CDN-Verizon Premium 规则引擎的条件表达式
description: Verizon Premium 规则引擎中 Azure CDN 的参考文档与条件和功能匹配。
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: d28bda28894477845c2a050666b3b4dd332b7d50
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082975"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-conditional-expressions"></a>Verizon 高级规则引擎条件表达式中的 Azure CDN

本文列出了 Azure 内容分发网络（CDN）[规则引擎](cdn-verizon-premium-rules-engine.md)的条件表达式的详细说明。

规则的第一部分是条件表达式。

条件表达式 | 说明
-----------------------|-------------
IF | IF 表达式始终是规则中第一个声明的一部分。 与其他所有条件表达式一样，这个 IF 声明必须与匹配项关联。 如果未定义任何其他条件表达式，则此匹配项将确定在将一组功能应用于请求之前必须满足的条件。
AND IF | AND IF 表达式仅可添加在以下类型的条件表达式之后：IF、AND IF。 它指示初始 IF 语句必须满足其他条件。
ELSE IF| ELSE IF 表达式指定在一组特定于此 ELSE IF 语句的功能发生之前必须满足的替代条件。 存在 ELSE IF 语句即表示之前语句已结束。 可放在 ELSE IF 语句之后的唯一条件表达式是其他 ELSE IF 语句。 这意味着 ELSE IF 语句仅可用于指定必须满足的单个附加条件。

**示例**：![CDN 匹配条件](./media/cdn-rules-engine-reference/cdn-rules-engine-conditional-expression.png)

 > [!TIP]
   > 后一个规则可能会覆盖前一个规则所指定的操作。
   > 示例：全方位的规则通过基于令牌的身份验证保护所有请求。 在它下面可能会创建其他规则，以便对某种请求做例外处理。

## <a name="next-steps"></a>后续步骤

- [Azure CDN 概述](cdn-overview.md)
- [规则引擎参考](cdn-verizon-premium-rules-engine-reference.md)
- [规则引擎匹配条件](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [规则引擎功能](cdn-verizon-premium-rules-engine-reference-features.md)
- [使用规则引擎重写默认 HTTP 行为](cdn-verizon-premium-rules-engine.md)