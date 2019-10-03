---
title: Azure 数据工厂映射数据流筛选器转换
description: Azure 数据工厂映射数据流筛选器转换
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: e0b41850c149ff7095333cf77b780dec1f03b882
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66234413"
---
# <a name="azure-data-factory-filter-transformation"></a>Azure 数据工厂筛选器转换

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

筛选器转换提供行筛选功能。 生成定义筛选器条件的表达式。 单击文本框，启动表达式生成器。 在表达式生成器中，生成筛选器表达式，控制当前数据流中允许传递（筛选）到下一转换的行。 将 SQL 语句的 WHERE 子句的筛选器转换。

## <a name="filter-on-loanstatus-column"></a>对 loan_status 列进行筛选：

```
in([‘Default’, ‘Charged Off’, ‘Fully Paid’], loan_status).
```

“电影”演示的“年份”列中的筛选器：

```
year > 1980
```

## <a name="next-steps"></a>后续步骤

请尝试筛选转换，一个列[选择转换](data-flow-select.md)
