---
title: 映射数据流中的筛选转换
description: 使用 Azure 数据工厂映射数据流中的筛选器转换筛选出行
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/16/2019
ms.openlocfilehash: 84b935d1646ea4f13d5926fc68797fd8efd03dea
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930360"
---
# <a name="filter-transformation-in-mapping-data-flow"></a>映射数据流中的筛选转换

筛选器转换允许基于条件进行行筛选。 输出流包括与筛选条件匹配的所有行。 筛选器转换类似于 SQL 中的 WHERE 子句。

## <a name="configuration"></a>配置

使用 "数据流表达式生成器" 输入筛选条件的表达式。 若要打开 "表达式生成器"，请单击蓝色框。 筛选条件的类型必须为布尔值。 有关如何创建表达式的详细信息，请参阅[表达式生成器](concepts-data-flow-expression-builder.md)文档。

![筛选转换](media/data-flow/filter1.png "筛选转换")

## <a name="data-flow-script"></a>数据流脚本

### <a name="syntax"></a>语法

```
<incomingStream>
    filter(
        <conditionalExpression>
    ) ~> <filterTransformationName>
```

### <a name="example"></a>示例

下面的示例是一个名为 `FilterBefore1960` 的筛选器转换，它采用传入流 `CleanData`。 筛选条件是 `year <= 1960`的表达式。

在数据工厂 UX 中，此转换如下图所示：

![筛选转换](media/data-flow/filter1.png "筛选转换")

此转换的数据流脚本位于下面的代码片段中：

```
CleanData
    filter(
        year <= 1960
    ) ~> FilterBefore1960

```

## <a name="next-steps"></a>后续步骤

用[select 转换](data-flow-select.md)筛选出列
