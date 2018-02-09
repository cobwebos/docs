---
title: "可用于 Azure 机器学习数据准备的示例筛选表达式 | Microsoft Docs"
description: "本文档提供了一组可用于 Azure 机器学习数据准备的筛选表达式示例"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: df78e871625f4de406de5ba4ae0fea327b87b01e
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2018
---
# <a name="sample-of-filter-expressions-python"></a>筛选表达式示例 (Python) 
阅读本附录前，请先阅读 [Python 扩展性概述](data-prep-python-extensibility-overview.md)。

## <a name="filter-with-equivalence-test"></a>等效性测试筛选
仅筛选 Col2 的值（数字）大于 4 的行。 

```python
    row["Col2"] > 4
```

## <a name="filter-with-multiple-columns"></a>多列筛选 
仅筛选 Col1 包含值“Good”并且 Col2 包含（数字）值 1 的行。 
```python
    row["Col1"] == 'Good' and row["Col2"] == 1
```

## <a name="test-filter-against-null"></a>针对 null 测试筛选器
仅筛选 Col1 包含 null 值的行。 
```python
    pd.isnull(row["Col1"])
```
