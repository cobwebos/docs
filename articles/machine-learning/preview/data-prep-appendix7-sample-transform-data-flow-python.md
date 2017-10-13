---
title: "可用于 Azure 机器学习数据准备的转换数据流转换示例 | Microsoft Docs"
description: "本文档提供一组可用于 Azure ML 数据准备的转换数据流转换示例"
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
ms.openlocfilehash: f43f65ca89349fc790684e9bd7acd2f19e15abe5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="sample-of-custom-data-flow-transforms-python"></a>自定义数据流转换示例 (Python) 
此转换在菜单中的名称为“转换数据流（脚本）” 阅读本附录前，先阅读 [Python 扩展性概述](data-prep-python-extensibility-overview.md)

## <a name="transform-frame"></a>转换帧
### <a name="create-a-new-column-dynamically"></a>动态新建列 
动态创建一个列 (city2)，然后将 San Francisco 的多个不同版本协调为现有 city 列中的某个版本。
```python
    df.loc[(df['city'] == 'San Francisco') | (df['city'] == 'SF') | (df['city'] == 'S.F.') | (df['city'] == 'SAN FRANCISCO'), 'city2'] = 'San Francisco'
```

### <a name="add-new-aggregates"></a>添加新的聚合
使用通过 first 和 last 聚合函数针对 score 列（按 risk_category 列分组）计算所得的结果新建帧
```python
    df = df.groupby(['risk_category'])['Score'].agg(['first','last'])
```
### <a name="winsorize-a-column"></a>对列进行去极值处理 
重新表述数据，满足减少列中离群值的公式的要求
```python
    import scipy.stats as stats
    df['Last Order'] = stats.mstats.winsorize(df['Last Order'].values, limits=0.4)
```

## <a name="transform-data-flow"></a>转换数据流
### <a name="fill-down"></a>向下填充 
向下填充需要两个转换。
假设数据如下所示：


|状态         |城市       |
|--------------|-----------|
|Washington    |Redmond    |
|              |Bellevue   |
|              |Issaquah   |
|              |西雅图    |
|California    |洛杉矶|
|              |San Diego  |
|              |San Jose   |
|Texas         |达拉斯     |
|              |San Antonio|
|              |Houston    |

首先创建包含以下代码的“添加列（脚本）”转换
```python
    row['State'] if len(row['State']) > 0 else None
```
现在创建包含以下代码的转换数据流（脚本）转换
```python
    df = df.fillna( method='pad')
```

数据现在如下所示：

|状态         |newState         |城市       |
|--------------|--------------|-----------|
|Washington    |Washington    |Redmond    |
|              |Washington    |Bellevue   |
|              |Washington    |Issaquah   |
|              |Washington    |西雅图    |
|California    |California    |洛杉矶|
|              |California    |San Diego  |
|              |California    |San Jose   |
|Texas         |Texas         |达拉斯     |
|              |Texas         |San Antonio|
|              |Texas         |Houston    |

