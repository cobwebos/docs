---
title: 映射数据流中的有条件拆分转换
description: 使用 Azure 数据工厂映射数据流中的有条件拆分转换将数据拆分为不同的流
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/16/2019
ms.openlocfilehash: bd9241e526d7cf42f0697afb8635c085a08c80d8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81606483"
---
# <a name="conditional-split-transformation-in-mapping-data-flow"></a>映射数据流中的有条件拆分转换

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

有条件拆分转换根据匹配条件将数据行路由到不同的流。 有条件拆分转换类似于编程语言中的 CASE 决策结构。 转换计算表达式，并根据结果将数据行定向到指定的流。

## <a name="configuration"></a>配置

"**拆分依据**" 设置确定数据行是流向第一个匹配流还是传递到其匹配的每个流。

使用数据流表达式生成器为拆分条件输入表达式。 若要添加新条件，请单击现有行中的加号图标。 还可以为与任何条件都不匹配的行添加默认流。

![有条件拆分](media/data-flow/conditionalsplit1.png "有条件拆分选项")

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

下面的示例是一个名为`SplitByYear`的有条件拆分转换，它`CleanData`采用传入流。 此转换有两个拆分`year < 1960`条件`year > 1980`和。 `disjoint`为 false，因为数据转到第一个匹配的条件。 每个匹配第一个条件的行都会转`moviesBefore1960`到输出流。 与第二个条件匹配的所有剩余行都`moviesAFter1980`将移到输出流中。 所有其他行都流过默认流`AllOtherMovies`。

在数据工厂 UX 中，此转换如下图所示：

![有条件拆分](media/data-flow/conditionalsplit1.png "有条件拆分选项")

此转换的数据流脚本位于下面的代码片段中：

```
CleanData
    split(
        year < 1960,
        year > 1980,
        disjoint: false
    ) ~> SplitByYear@(moviesBefore1960, moviesAfter1980, AllOtherMovies)
```

## <a name="next-steps"></a>后续步骤

用于 "有条件拆分" 的常见数据流转换为[联接转换](data-flow-join.md)、[查找转换](data-flow-lookup.md)和[选择转换](data-flow-select.md)
