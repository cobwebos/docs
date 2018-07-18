---
title: Azure CDN 规则引擎条件表达式 | Microsoft Docs
description: Azure CDN 规则引擎匹配条件和功能的参考文档。
services: cdn
documentationcenter: ''
author: Lichard
manager: akucer
editor: ''
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: rli
ms.openlocfilehash: 57e56c38e003cb83dcf44f455c4451d159db8a59
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
ms.locfileid: "22997972"
---
# <a name="azure-cdn-rules-engine-conditional-expressions"></a>Azure CDN 规则引擎条件表达式
本主题列出了 Azure 内容传送网络 (CDN) [规则引擎](cdn-rules-engine.md)的条件表达式的详细说明。

规则的第一部分是条件表达式。

条件表达式 | 说明
-----------------------|-------------
IF | IF 表达式始终是规则中第一个声明的一部分。 与其他所有条件表达式一样，这个 IF 声明必须与匹配项关联。 如果未定义其他条件表达式，则此匹配项确定在一组功能应用到请求之前必须满足的条件。
AND IF | AND IF 表达式仅可添加在以下类型的条件表达式之后：IF、AND IF。 它指示初始 IF 语句必须满足其他条件。
ELSE IF| ELSE IF 表达式指定在一组特定于此 ELSE IF 语句的功能发生之前必须满足的替代条件。 存在 ELSE IF 语句即表示之前语句已结束。 可放在 ELSE IF 语句之后的唯一条件表达式是其他 ELSE IF 语句。 这意味着 ELSE IF 语句仅可用于指定必须满足的单个附加条件。

**示例**：![CDN 匹配条件](./media/cdn-rules-engine-reference/cdn-rules-engine-conditional-expression.png)

 > [!TIP]
   > 后一个规则可能会覆盖前一个规则所指定的操作。 示例：全方位的规则通过基于令牌的身份验证保护所有请求。 在它下面可能会创建其他规则，以便对某种请求做例外处理。

### <a name="next-steps"></a>后续步骤
* [Azure CDN 概述](cdn-overview.md)
* [规则引擎参考](cdn-rules-engine-reference.md)
* [规则引擎匹配条件](cdn-rules-engine-reference-match-conditions.md)
* [规则引擎功能](cdn-rules-engine-reference-features.md)
* [使用规则引擎重写默认 HTTP 行为](cdn-rules-engine.md)
