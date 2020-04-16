---
title: 映射数据流的筛选器转换
description: 使用 Azure 数据工厂映射数据流的筛选器转换筛选出行
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/16/2019
ms.openlocfilehash: 3460c789909c6acd0537ef89a9ec8509bf6e135d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413729"
---
# <a name="filter-transformation-in-mapping-data-flow"></a>映射数据流的筛选器转换

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

筛选器转换允许基于条件进行行筛选。 输出流包括与筛选条件匹配的所有行。 筛选器转换类似于 SQL 中的 WHERE 子句。

## <a name="configuration"></a>配置

使用数据流表达式生成器为筛选器条件输入表达式。 要打开表达式生成器，请单击蓝色框。 过滤器条件必须为布尔类型。 有关如何创建表达式的详细信息，请参阅[表达式生成器](concepts-data-flow-expression-builder.md)文档。

![过滤器转换](media/data-flow/filter1.png "过滤器转换")

## <a name="data-flow-script"></a>数据流脚本

### <a name="syntax"></a>语法

```
<incomingStream>
    filter(
        <conditionalExpression>
    ) ~> <filterTransformationName>
```

### <a name="example"></a>示例

下面的示例是一个筛选器转换，`FilterBefore1960`该转换名为，用于`CleanData`传入流。 筛选器条件是表达式`year <= 1960`。

在数据工厂 UX 中，此转换类似于下图：

![过滤器转换](media/data-flow/filter1.png "过滤器转换")

此转换的数据流脚本位于下面的代码段中：

```
CleanData
    filter(
        year <= 1960
    ) ~> FilterBefore1960

```

## <a name="next-steps"></a>后续步骤

使用[选择变换](data-flow-select.md)筛选出列
