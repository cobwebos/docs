---
title: Azure 数据工厂映射数据流筛选器转换
description: Azure 数据工厂映射数据流筛选器转换
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: 2afe079c346a15ec212664ce022ac5e2926b12d4
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387814"
---
# <a name="azure-data-factory-filter-transformation"></a>Azure 数据工厂筛选器转换



筛选器转换提供行筛选功能。 生成定义筛选器条件的表达式。 单击文本框，启动表达式生成器。 在表达式生成器中，生成筛选器表达式，控制当前数据流中允许传递（筛选）到下一转换的行。 将筛选器转换视为 SQL 语句的 WHERE 子句。

## <a name="filter-on-loan_status-column"></a>筛选 loan_status 列：

```
in([‘Default’, ‘Charged Off’, ‘Fully Paid’], loan_status).
```

“电影”演示的“年份”列中的筛选器：

```
year > 1980
```

## <a name="next-steps"></a>后续步骤

尝试列筛选转换，[选择转换](data-flow-select.md)
