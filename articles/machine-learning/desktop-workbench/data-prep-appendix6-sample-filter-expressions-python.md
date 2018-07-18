---
title: 可用于 Azure 机器学习数据准备的示例筛选表达式 | Microsoft Docs
description: 本文档提供了一组可用于 Azure 机器学习数据准备的筛选表达式示例
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: 434da595762a077b94ff034325d5bdb156585884
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2018
ms.locfileid: "34830354"
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
