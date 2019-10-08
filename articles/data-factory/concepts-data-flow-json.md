---
title: Azure 数据工厂映射数据流 JSON 概念
description: 数据工厂映射数据流提供了用层次结构处理 JSON 文档的内置功能
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 37db3e153e8dfcbc1120fcb1f6d2f77187edc78e
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029665"
---
# <a name="mapping-data-flow-json-handling"></a>映射数据流 JSON 处理



## <a name="creating-json-structures-in-expression-editor"></a>在表达式编辑器中创建 JSON 结构
### <a name="derived-column-transformation"></a>派生列转换
通过 "派生列表达式编辑器"，可以更轻松地将复杂列添加到数据流。 添加新列并打开编辑器后，有两个选项：手动输入 JSON 结构，或使用 UI 以交互方式添加个子列。

#### <a name="interactive-ui-json-design"></a>交互式 UI JSON 设计
在输出架构端窗格中，可以使用 `+` 菜单添加新的个子列：![添加 subcolumn](media/data-flow/addsubcolumn.png "添加 subcolumn")

可以通过相同的方式添加新的列和个子列。 对于每个非复杂字段，可以在右侧的表达式编辑器中添加表达式。

![复杂列](media/data-flow/complexcolumn.png "复杂列")

#### <a name="manual-json-design"></a>手动 JSON 设计
若要手动添加 JSON 结构，请添加一个新列，然后在编辑器中输入该表达式。 表达式采用以下常规格式：
```
@(
    field1=0,
    field2=@(
        field1=0
    )
)
```
如果为名为 "complexColumn" 的列输入了此表达式，则会将其作为以下 JSON 写入接收器：
```
{
    "complexColumn": {
        "field1": 0,
        "field2": {
            "field1": 0
        }
    }
}
```

#### <a name="sample-manual-script-for-complete-hierarchical-definition"></a>完整层次结构定义的示例手动脚本
```
@(
    title=Title,
    firstName=FirstName,
    middleName=MiddleName,
    lastName=LastName,
    suffix=Suffix,
    contactDetails=@(
        email=EmailAddress,
        phone=Phone
    ),
    address=@(
        line1=AddressLine1,
        line2=AddressLine2,
        city=City,
        state=StateProvince,
        country=CountryRegion,
        postCode=PostalCode
    ),
    ids=[
        toString(CustomerID), toString(AddressID), rowguid
    ]
)
```

## <a name="source-format-options"></a>源格式选项
### <a name="default"></a>默认
```
{ "json": "record 1" }
{ "json": "record 2" }
{ "json": "record 3" }
```

### <a name="single-document"></a>单个文档
* 选项一
```
[
    {
        "json": "record 1"
    },
    {
        "json": "record 2"
    },
    {
        "json": "record 3"
    }
]
```

* 选项二
```
File1.json
{
    "json": "record 1"
}
File2.json
{
    "json": "record 2"
}
File3.json
{
    "json": "record 3"
}
```

### <a name="unquoted-column-names"></a>不带引号的列名
```
{ json: "record 1" }
{ json: "record 2" }
{ json: "record 3" }
```

### <a name="has-comments"></a>包含注释
```
{ "json": /** comment **/ "record 1" }
{ "json": "record 2" }
{ /** comment **/ "json": "record 3" }
```

### <a name="single-quoted"></a>单引号
```
{ 'json': 'record 1' }
{ 'json': 'record 2' }
{ 'json': 'record 3' }
```

### <a name="backslash-escaped"></a>反斜杠转义
```
{ "json": "record 1" }
{ "json": "\} \" \' \\ \n \\n record 2" }
{ "json": "record 3" }
```

## <a name="higher-order-functions"></a>高阶函数
## <a name="filter"></a>filter
筛选出数组中的元素，这些元素不满足提供的谓词。 筛选器需要引用谓词函数中的一个元素作为 #item。

### <a name="examples"></a>示例
```
filter([1, 2, 3, 4], #item > 2) => [3, 4]
filter(['a', 'b', 'c', 'd'], #item == 'a' || #item == 'b') => ['a', 'b']
```

## <a name="map"></a>map
使用提供的表达式将数组的每个元素映射到新元素。 Map 需要对 expression 函数中的一个元素引用 #item。

### <a name="examples"></a>示例
```
map([1, 2, 3, 4], #item + 2) => [3, 4, 5, 6]
map(['a', 'b', 'c', 'd'], #item + '_processed') => ['a_processed', 'b_processed', 'c_processed', 'd_processed']
```

## <a name="reduce"></a>降
累积数组中的元素。 减少需要引用第一个表达式函数中的累加器和一个元素作为 #acc 和 #item，并且它希望在第二个表达式函数中使用 #result 的结果值。

### <a name="examples"></a>示例
```
reduce([1, 2, 3, 4], 0, #acc + #item, #result) => 10
reduce(['1', '2', '3', '4'], '0', #acc + #item, #result) => '01234'
reduce([1, 2, 3, 4], 0, #acc + #item, #result + 15) => 25
```

## <a name="sort"></a>sort
使用提供的谓词函数对数组进行排序。 排序需要引用 expression 函数中的两个连续元素，#item1 和 #item2。

### <a name="examples"></a>示例
```
sort([4, 8, 2, 3], compare(#item1, #item2)) => [2, 3, 4, 8]
sort(['a3', 'b2', 'c1'],
        iif(right(#item1, 1) >= right(#item2, 1), 1, -1)) => ['c1', 'b2', 'a3']
sort(['a3', 'b2', 'c1'],
        iif(#item1 >= #item2, 1, -1)) => ['a3', 'b2', 'c1']
```

## <a name="contains"></a>contains
如果所提供的数组中的任何元素在提供的谓词中计算为 true，则返回 true。 Contains 需要引用谓词函数中的一个元素作为 #item。

### <a name="examples"></a>示例
```
contains([1, 2, 3, 4], #item == 3) => true
contains([1, 2, 3, 4], #item > 5) => false
```

## <a name="next-steps"></a>后续步骤

* [使用派生列转换构建你的层次结构](data-flow-derived-column.md)
