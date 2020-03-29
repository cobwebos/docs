---
title: 映射数据流中的条件拆分转换
description: 使用 Azure 数据工厂映射数据流中的条件拆分转换将数据拆分到不同的流中
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/16/2019
ms.openlocfilehash: d7e2af6c98951e685192656b37226716e4340bfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930449"
---
# <a name="conditional-split-transformation-in-mapping-data-flow"></a>映射数据流中的条件拆分转换

条件拆分转换根据匹配条件将数据行路由到不同的流。 条件拆分转换类似于编程语言中的 CASE 决策结构。 转换计算表达式，并根据结果将数据行定向到指定的流。

## <a name="configuration"></a>Configuration

"**拆分"** 设置确定数据行是流向第一个匹配流还是与其匹配的每个流。

使用数据流表达式生成器为拆分条件输入表达式。 要添加新条件，请单击现有行中的加号图标。 对于与任何条件不匹配的行，也可以添加默认流。

![条件拆分](media/data-flow/conditionalsplit1.png "条件拆分选项")

## <a name="data-flow-script"></a>数据流脚本

### <a name="syntax"></a>语法

```
<incomingStream>
    split(
        <conditionalExpression1>
        <conditionalExpression2>
        ...
        disjoint: {true | false}
    ) ~> <splitTx>@(stream1, stream2, ..., <defaultStream>)
```

### <a name="example"></a>示例

下面的示例是一个条件拆分转换，`SplitByYear`该转换名为，用于`CleanData`传入流。 此转换有两个拆分`year < 1960`条件和`year > 1980`。 `disjoint`为 false，因为数据转到第一个匹配条件。 匹配第一个条件的每一行都转到`moviesBefore1960`输出流。 匹配第二个条件的所有剩余行都转到输出`moviesAFter1980`流。 所有其他行流经默认流`AllOtherMovies`。

在数据工厂 UX 中，此转换类似于下图：

![条件拆分](media/data-flow/conditionalsplit1.png "条件拆分选项")

此转换的数据流脚本位于下面的代码段中：

```
CleanData
    split(
        year < 1960,
        year > 1980,
        disjoint: false
    ) ~> SplitByYear@(moviesBefore1960, moviesAfter1980, AllOtherMovies)
```

## <a name="next-steps"></a>后续步骤

与条件拆分一起使用的常见数据流转换是[联接转换](data-flow-join.md)、[查找转换](data-flow-lookup.md)和[选择转换](data-flow-select.md)
