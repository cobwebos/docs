---
title: 映射数据流中的查找转换
description: 使用映射数据流中的查找转换从另一个源引用数据。
author: kromerm
ms.reviewer: daperlov
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/23/2020
ms.openlocfilehash: 672fecc7487a73909efa5b4247f4889bb47b7b7e
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594311"
---
# <a name="lookup-transformation-in-mapping-data-flow"></a>映射数据流中的查找转换

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

使用查找转换可以引用数据流流中其他源的数据。 查找转换会将匹配数据中的列追加到源数据中。

查找转换类似于左外部联接。 主流中的所有行都将存在于包含查找流中的其他列的输出流中。 

## <a name="configuration"></a>配置

![查找转换](media/data-flow/lookup1.png "查找")

**主流：** 传入的数据流。 此流等效于联接的左侧。

**查找流：** 追加到主流中的数据。 要添加的数据是由查找条件确定的。 此流等效于联接的右侧。

**匹配多行：** 如果启用，则主流中具有多个匹配项的行将返回多个行。 否则，只会根据 "Match on" 条件返回单个行。

**匹配项：** 仅当未选择 "匹配多行" 时可见。 选择是否匹配任意行、第一个匹配项或最后一个匹配项。 建议使用任何行，因为它执行速度最快。 如果选择了第一行或最后一行，则需要指定排序条件。

**查找条件：** 选择要匹配的列。 如果满足相等条件，则将这些行视为匹配项。 悬停并选择 "计算列" 以使用数据流[表达式语言](data-flow-expression-functions.md)提取值。

查找转换只支持相等匹配。 若要自定义查找表达式以包含其他运算符（例如大于），建议使用[联接转换中的交叉联接](data-flow-join.md#custom-cross-join)。 交叉联接将避免执行任何可能出现笛卡尔积错误。

输出数据中包含两个流中的所有列。 若要删除重复列或不需要的列，请在查找转换后添加[select 转换](data-flow-select.md)。 还可以在接收器转换中删除或重命名列。

## <a name="analyzing-matched-rows"></a>分析匹配的行

查找转换后，可以使用函数`isMatch()`来查看各个行的查找是否匹配。

![查找模式](media/data-flow/lookup111.png "查找模式")

此模式的一个示例是使用有条件拆分转换来对`isMatch()`函数进行拆分。 在上面的示例中，匹配行经过最顶部的流，而非匹配行流过```NoMatch```流。

## <a name="testing-lookup-conditions"></a>测试查找条件

在调试模式下使用数据预览测试查找转换时，请使用一组少量的已知数据。 在对大型数据集中的行进行采样时，无法预测将读取哪些行和键用于测试。 结果是非确定性的，这意味着联接条件可能不会返回任何匹配项。

## <a name="broadcast-optimization"></a>广播优化

![广播联接](media/data-flow/broadcast.png "广播联接")

在联接、查找和存在转换中，如果一个或两个数据流适合工作节点内存，则可以通过启用**广播**来优化性能。 默认情况下，spark 引擎将自动决定是否广播一方。 若要手动选择要广播的端，请选择 "**固定**"。

不建议禁用通过**Off**选项进行的广播，除非您的联接遇到超时错误。

## <a name="data-flow-script"></a>数据流脚本

### <a name="syntax"></a>语法

```
<leftStream>, <rightStream>
    lookup(
        <lookupConditionExpression>,
        multiple: { true | false },
        pickup: { 'first' | 'last' | 'any' },  ## Only required if false is selected for multiple
        { desc | asc }( <sortColumn>, { true | false }), ## Only required if 'first' or 'last' is selected. true/false determines whether to put nulls first
        broadcast: { 'auto' | 'left' | 'right' | 'both' | 'off' }
    ) ~> <lookupTransformationName>
```
### <a name="example"></a>示例

![查找转换](media/data-flow/lookup-dsl-example.png "查找")

以上查找配置的数据流脚本位于下面的代码段中。

```
SQLProducts, DimProd lookup(ProductID == ProductKey,
    multiple: false,
    pickup: 'first',
    asc(ProductKey, true),
    broadcast: 'auto')~> LookupKeys
```
## 
后续步骤

* [联接](data-flow-join.md)和[exists](data-flow-exists.md)转换均采用多个流输入
* 使用```isMatch()```有[条件拆分转换](data-flow-conditional-split.md)来拆分匹配和不匹配值的行
