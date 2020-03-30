---
title: 映射数据流的查找转换
description: 在映射数据流时使用查找转换从其他源引用数据。
author: kromerm
ms.reviewer: daperlov
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/23/2020
ms.openlocfilehash: 78c6c1363af011a90865770d88c0037e50e958c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240416"
---
# <a name="lookup-transformation-in-mapping-data-flow"></a>映射数据流的查找转换

使用查找转换引用来自数据流中另一个源的数据。 查找转换将列从匹配的数据追加到源数据。

查找转换类似于左外部联接。 主流中的所有行都将存在于输出流中，并包含来自查找流的其他列。 

## <a name="configuration"></a>Configuration

![查找转换](media/data-flow/lookup1.png "查找")

**主流：** 传入的数据流。 此流等效于联接的左侧。

**查找流：** 追加到主流的数据。 添加哪些数据由查找条件决定。 此流等效于联接的右侧。

**匹配多行：** 如果启用，主流中具有多个匹配项的行将返回多行。 否则，将仅根据"匹配"条件返回一行。

**匹配：** 仅当启用了"匹配多行"时，才可见。 选择是匹配任何行、第一个匹配项还是最后一个匹配项。 建议任何行执行最快。 如果选择了第一行或最后一行，则需要指定排序条件。

**查找条件：** 选择要匹配的列。 如果满足相等条件，则行将被视为匹配项。 悬停并选择"计算列"以使用[数据流表达式语言](data-flow-expression-functions.md)提取值。

查找转换仅支持相等匹配。 要自定义查找表达式以包括其他运算符（如大于，建议[在联接转换中使用交叉联接](data-flow-join.md#custom-cross-join)）。 交叉联接将避免执行时可能出现的点菜产品错误。

输出数据中包含来自两个流的所有列。 要删除重复列或不需要的列，请在查找转换后添加[选择转换](data-flow-select.md)。 列也可以在接收器转换中删除或重命名。

## <a name="analyzing-matched-rows"></a>分析匹配的行

查找转换后，该函数`isMatch()`可用于查看查找是否匹配单个行。

![查找模式](media/data-flow/lookup111.png "查找模式")

此模式的一个示例是使用条件拆分转换在`isMatch()`函数上拆分。 在上面的示例中，匹配的行通过顶部流，不匹配的行流通过```NoMatch```流。

## <a name="testing-lookup-conditions"></a>测试查找条件

在调试模式下使用数据预览测试查找转换时，请使用一小组已知数据。 从大型数据集对行进行采样时，无法预测将读取哪些行和键进行测试。 结果是非确定性的，这意味着您的联接条件可能不会返回任何匹配项。

## <a name="broadcast-optimization"></a>广播优化

在 Azure 数据工厂中，映射数据流在横向扩展的 Spark 环境中执行。 如果数据集可以适合辅助节点内存空间，则可以通过启用广播来优化查找性能。

![广播加入](media/data-flow/broadcast.png "广播加入")

启用广播会将整个数据集推送到内存中。 对于仅包含几千行的小型数据集，广播可以大大提高查找性能。 对于大型数据集，此选项可能导致内存不足异常。

## <a name="data-flow-script"></a>数据流脚本

### <a name="syntax"></a>语法

```
<leftStream>, <rightStream>
    lookup(
        <lookupConditionExpression>,
        multiple: { true | false },
        pickup: { 'first' | 'last' | 'any' },  ## Only required if false is selected for multiple
        { desc | asc }( <sortColumn>, { true | false }), ## Only required if 'first' or 'last' is selected. true/false determines whether to put nulls first
        broadcast: { 'none' | 'left' | 'right' | 'both' }
    ) ~> <lookupTransformationName>
```
### <a name="example"></a>示例

![查找转换](media/data-flow/lookup-dsl-example.png "查找")

上述查找配置的数据流脚本位于下面的代码段中。

```
SQLProducts, DimProd lookup(ProductID == ProductKey,
    multiple: false,
    pickup: 'first',
    asc(ProductKey, true),
    broadcast: 'none')~> LookupKeys
```
## 
后续步骤

* [联接](data-flow-join.md)和[存在](data-flow-exists.md)转换都采用多个流输入
* 使用[条件拆分转换](data-flow-conditional-split.md)与```isMatch()```在匹配值和非匹配值上拆分行
