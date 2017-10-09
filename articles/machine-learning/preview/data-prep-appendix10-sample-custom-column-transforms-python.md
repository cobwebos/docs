---
title: "用于在 Azure 机器学习数据准备中派生新列的 Python 示例 | Microsoft Docs"
description: "本文档提供用于在 Azure ML 数据准备中新建列的 Python 代码示例"
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
ms.date: 09/12/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 9a6e331e622b007232a62b34c2220d60d1d050ca
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---

# <a name="sample-of-custom-column-transforms-python"></a>自定义列转换示例 (Python) 
此转换在菜单中的名称为“添加列（脚本）”

阅读本附录前，先阅读 [Python 扩展性概述](data-prep-python-extensibility-overview.md)

## <a name="test-equivalence-and-replace-values"></a>测试等效值和替换值 
如果 Col1 中的值小于 4，则新列应该具有值 1，否则应该具有值 2 

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
如果 Col1 包含 null 值，则将新列标记为错误，否则标记为正常 

```python
    'Bad' if pd.isnull(row["Col1"]) else 'Good'
```
## <a name="new-computed-column"></a>新计算列 
```python
    np.log(row["Col1"])
```
## <a name="epoch-computation"></a>Epoch 计算 
自 Unix Epoch 起经过的秒数（假设 Col1 已经是日期） 
```python
    row["Col1"] - datetime.datetime.utcfromtimestamp(0)).total_seconds()
```






