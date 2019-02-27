---
title: Azure 数据工厂映射数据流筛选器转换
description: Azure 数据工厂映射数据流筛选器转换
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: b7e7b123560aae3a2d3086c8536969297d31f7ba
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2019
ms.locfileid: "56270890"
---
# <a name="azure-data-factory-mapping-data-flow-filter-transformation"></a>Azure 数据工厂映射数据流筛选器转换

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

筛选器转换提供行筛选功能。 生成定义筛选器条件的表达式。 单击文本框，启动表达式生成器。 在表达式生成器中，生成筛选器表达式，控制当前数据流中允许传递（筛选）到下一转换的行。

例如，loan_status 列中的筛选器：

```
in([‘Default’, ‘Charged Off’, ‘Fully Paid’], loan_status).
```

“电影”演示的“年份”列中的筛选器：

```
year > 1980
```
