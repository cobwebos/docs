---
title: 在 Azure 数据工厂中映射数据流中使用 JSON
description: Azure 数据工厂映射数据流提供了用层次结构处理 JSON 文档的内置功能
author: kromerm
ms.author: makromer
ms.review: djpmsft
ms.service: data-factory
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: fe412e9e682fb55f1664c546e6b6c5a347527adb
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "72387347"
---
# <a name="mapping-data-flow-json-handling"></a>映射数据流 JSON 处理

## <a name="creating-json-structures-in-derived-column"></a>在派生列中创建 JSON 结构

您可以通过派生列表达式生成器将复杂列添加到数据流。 在派生列转换中，通过单击蓝色框来添加一个新列并打开 "表达式生成器"。 若要使列复杂化，可以手动输入 JSON 结构，或使用 UX 以交互方式添加个子列。

### <a name="using-the-expression-builder-ux"></a>使用表达式生成器 UX

在 "输出架构" 侧窗格中，将鼠标悬停在某一列上，并单击加号图标。 选择 "**添加 subcolumn** "，使列成为复杂类型。

![添加 subcolumn](media/data-flow/addsubcolumn.png "添加 Subcolumn")

您可以通过相同的方式添加更多的列和个子列。 对于每个非复杂字段，可以在右侧的表达式编辑器中添加表达式。

![复杂列](media/data-flow/complexcolumn.png "复杂列")

### <a name="entering-the-json-structure-manually"></a>手动输入 JSON 结构

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

使用 JSON 数据集作为数据流中的源，可设置五个附加设置。 这些设置可在 "**源选项**" 选项卡中的**JSON 设置**可折叠项下找到。  

![JSON 设置](media/data-flow/json-settings.png "JSON 设置")

### <a name="default"></a>默认

默认情况下，使用以下格式读取 JSON 数据。

```
{ "json": "record 1" }
{ "json": "record 2" }
{ "json": "record 3" }
```

### <a name="single-document"></a>单个文档

如果选择了 "**单个文档**"，则映射数据流会从每个文件读取一个 JSON 文档。 

``` json
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

如果选择了不带**引号的列名称**，则映射数据流将读取未括在引号中的 JSON 列。 

```
{ json: "record 1" }
{ json: "record 2" }
{ json: "record 3" }
```

### <a name="has-comments"></a>包含注释

如果 JSON 数据具有 C 或C++样式注释，请选择 "**具有注释**"。

``` json
{ "json": /** comment **/ "record 1" }
{ "json": "record 2" }
{ /** comment **/ "json": "record 3" }
```

### <a name="single-quoted"></a>单引号

如果 JSON 字段和值使用单引号而不是双引号，则选择 "**单引号**"。

```
{ 'json': 'record 1' }
{ 'json': 'record 2' }
{ 'json': 'record 3' }
```

### <a name="backslash-escaped"></a>反斜杠转义

如果使用反斜杠对 JSON 数据中的字符进行转义，则选择 "**单引号**"。

```
{ "json": "record 1" }
{ "json": "\} \" \' \\ \n \\n record 2" }
{ "json": "record 3" }
```

## <a name="higher-order-functions"></a>高阶函数

高阶函数是采用一个或多个函数作为参数的函数。 下面列出了在映射启用数组操作的数据流时支持的高阶函数。

### <a name="filter"></a>filter
筛选出数组中的元素，这些元素不满足提供的谓词。 筛选器需要引用谓词函数中的一个元素作为 #item。

#### <a name="examples"></a>示例
```
filter([1, 2, 3, 4], #item > 2) => [3, 4]
filter(['a', 'b', 'c', 'd'], #item == 'a' || #item == 'b') => ['a', 'b']
```

### <a name="map"></a>map
使用提供的表达式将数组的每个元素映射到新元素。 Map 需要对 expression 函数中的一个元素引用 #item。

#### <a name="examples"></a>示例
```
map([1, 2, 3, 4], #item + 2) => [3, 4, 5, 6]
map(['a', 'b', 'c', 'd'], #item + '_processed') => ['a_processed', 'b_processed', 'c_processed', 'd_processed']
```

### <a name="reduce"></a>降
累积数组中的元素。 减少需要引用第一个表达式函数中的累加器和一个元素作为 #acc 和 #item，并且它希望在第二个表达式函数中使用 #result 的结果值。

#### <a name="examples"></a>示例
```
reduce([1, 2, 3, 4], 0, #acc + #item, #result) => 10
reduce(['1', '2', '3', '4'], '0', #acc + #item, #result) => '01234'
reduce([1, 2, 3, 4], 0, #acc + #item, #result + 15) => 25
```

### <a name="sort"></a>sort
使用提供的谓词函数对数组进行排序。 排序需要引用 expression 函数中的两个连续元素，#item1 和 #item2。

#### <a name="examples"></a>示例
```
sort([4, 8, 2, 3], compare(#item1, #item2)) => [2, 3, 4, 8]
sort(['a3', 'b2', 'c1'],
        iif(right(#item1, 1) >= right(#item2, 1), 1, -1)) => ['c1', 'b2', 'a3']
sort(['a3', 'b2', 'c1'],
        iif(#item1 >= #item2, 1, -1)) => ['a3', 'b2', 'c1']
```

### <a name="contains"></a>contains
如果所提供的数组中的任何元素在提供的谓词中计算为 true，则返回 true。 Contains 需要引用谓词函数中的一个元素作为 #item。

#### <a name="examples"></a>示例
```
contains([1, 2, 3, 4], #item == 3) => true
contains([1, 2, 3, 4], #item > 5) => false
```

## <a name="next-steps"></a>后续步骤

* [使用派生列转换构建你的层次结构](data-flow-derived-column.md)
