---
title: "用于在 Azure 机器学习数据准备中派生新列的 Python 示例 | Microsoft Docs"
description: "本文档提供用于在 Azure 机器学习数据准备中新建列的 Python 代码示例。"
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
ms.openlocfilehash: 128a4ae124f01a0514160450a3e9069b488274cd
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2018
---
# <a name="sample-of-custom-column-transforms-python"></a>自定义列转换示例 (Python) 
此转换在菜单中的名称为“添加列(脚本)”。

阅读本附录前，请先阅读 [Python 扩展性概述](data-prep-python-extensibility-overview.md)。

## <a name="test-equivalence-and-replace-values"></a>测试等效值和替换值 
如果 Col1 中的值小于 4，则新列应该具有值 1。 如果 Col1 中的值大于 4，则新列应该具有值 2。 

```python
    1 if row["Col1"] < 4 else 2
```
## <a name="current-date-and-time"></a>当前日期和时间 

```python
    datetime.datetime.now()
```
## <a name="typecasting"></a>类型转换 
```python
    float(row["Col1"]) / float(row["Col2"] - 1)
```
## <a name="evaluate-for-nullness"></a>求值是否为 null 
如果 Col1 包含 null 值，则将新列标记为“Bad”， 否则标记为“Good”。 

```python
    'Bad' if pd.isnull(row["Col1"]) else 'Good'
```
## <a name="new-computed-column"></a>新计算列 
```python
    np.log(row["Col1"])
```
## <a name="epoch-computation"></a>Epoch 计算 
自 Unix Epoch 起经过的秒数（假设 Col1 已经是日期）： 
```python
    row["Col1"] - datetime.datetime.utcfromtimestamp(0)).total_seconds()
```

## <a name="hash-a-column-value-into-a-new-column"></a>通过哈希算法将列值映射到新列
```python
    import hashlib
    hash(row["MyColumnToHashCol1"])

```




