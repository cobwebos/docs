---
title: "可用于 Azure 机器学习数据准备的示例筛选表达式 | Microsoft Docs"
description: "本文档提供了一组可用于 Azure ML 数据准备的筛选表达式示例"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/11/2017
ms.openlocfilehash: a80e43f84b518a7c9ce609fbebe34c531e1ab187
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="sample-of-filter-expressions-python"></a>筛选表达式示例 (Python) 
阅读本附录前，先阅读 [Python 扩展性概述](data-prep-python-extensibility-overview.md)

## <a name="filter-with-equivalence-test"></a>等效性测试筛选
仅筛选 Col2 的值（数字）大于 4 的行 

```python
    row["Col2"] > 4
```

## <a name="filter-with-multiple-columns"></a>多列筛选 
仅筛选 Col1 包含值“Good”并且 Col2 包含（数字）值 1 的行 
```python
    row["Col1"] == 'Good' and row["Col2"] == 1
```

## <a name="test-filter-against-null"></a>针对 null 测试筛选器
仅筛选 Col1 包含 null 值的行 
```python
    pd.isnull(row["Col1"])
```
