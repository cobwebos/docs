---
title: 映射数据流中的扁平转换
description: 使用拼合转换使分层数据非规范化
author: kromerm
ms.author: makromer
ms.review: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: a0e75957a0ab49394dab56f2b7fb847dee4b43cb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413680"
---
# <a name="flatten-transformation-in-mapping-data-flow"></a>映射数据流中的扁平转换

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

使用拼合变换在分层结构（如 JSON）中获取数组值，并将其展开到单独的行中。 此过程称为非规范化。

## <a name="configuration"></a>配置

扁平转换包含以下配置设置

![拼合设置](media/data-flow/flatten1.png "拼合设置")

### <a name="unroll-by"></a>展开者

选择要展开的数组。 每个数组中每个项的输出数据将具有一行。 如果输入行中的按数组展开为空，则将有一个输出行，其中未滚动的值为空。

### <a name="unroll-root"></a>展开根

默认情况下，拼合转换将数组解压到它存在的层次结构的顶部。 您可以选择数组作为展开根。 展开根必须是复杂对象的数组，这些对象是或包含按数组展开的。 如果选择了未卷根，则输出数据将包含卷根中每个项至少包含一行。 如果输入行在未滚动根中没有任何项，它将从输出数据中删除。 选择未卷根始终输出的行数小于或等于默认行为。

### <a name="flatten-mapping"></a>拼合映射

与选择转换类似，从传入字段和非规范化数组中选择新结构的投影。 如果映射了非规范化数组，则输出列将与数组的数据类型相同。 如果按数组展开是包含子数组的复杂对象的数组，则映射该 subarry 的项将输出数组。

请参阅检查选项卡和数据预览以验证映射输出。

## <a name="examples"></a>示例

有关压平变换的以下示例，请参阅以下 JSON 对象

``` json
{
  "name":"MSFT","location":"Redmond", "satellites": ["Bay Area", "Shanghai"],
  "goods": {
    "trade":true, "customers":["government", "distributer", "retail"],
    "orders":[
        {"orderId":1,"orderTotal":123.34,"shipped":{"orderItems":[{"itemName":"Laptop","itemQty":20},{"itemName":"Charger","itemQty":2}]}},
        {"orderId":2,"orderTotal":323.34,"shipped":{"orderItems":[{"itemName":"Mice","itemQty":2},{"itemName":"Keyboard","itemQty":1}]}}
    ]}}
{"name":"Company1","location":"Seattle", "satellites": ["New York"],
  "goods":{"trade":false, "customers":["store1", "store2"],
  "orders":[
      {"orderId":4,"orderTotal":123.34,"shipped":{"orderItems":[{"itemName":"Laptop","itemQty":20},{"itemName":"Charger","itemQty":3}]}},
      {"orderId":5,"orderTotal":343.24,"shipped":{"orderItems":[{"itemName":"Chair","itemQty":4},{"itemName":"Lamp","itemQty":2}]}}
    ]}}
{"name": "Company2", "location": "Bellevue",
  "goods": {"trade": true, "customers":["Bank"], "orders": [{"orderId": 4, "orderTotal": 123.34}]}}
{"name": "Company3", "location": "Kirkland"}
```

### <a name="no-unroll-root-with-string-array"></a>没有带字符串数组的解卷根

| 展开者 | 展开根 | 投影 |
| --------- | ----------- | ---------- |
| 货物.客户 | 无 | name <br> 客户 = 货物。 |

#### <a name="output"></a>输出

```
{ 'MSFT', 'government'}
{ 'MSFT', 'distributer'}
{ 'MSFT', 'retail'}
{ 'Company1', 'store'}
{ 'Company1', 'store2'}
{ 'Company2', 'Bank'}
{ 'Company3', null}
```

### <a name="no-unroll-root-with-complex-array"></a>没有具有复杂数组的卷根

| 展开者 | 展开根 | 投影 |
| --------- | ----------- | ---------- |
| 货物.订单.发货.订单项目 | 无 | name <br> 订单 Id = 货物.订单.订单Id <br> 项目名称 = 货物.订单.发货.订单项目.项目名称 <br> 物料数量 = 货物.订单.发货.订单项目.项目数量 <br> 位置 = 位置 |

#### <a name="output"></a>输出

```
{ 'MSFT', 1, 'Laptop', 20, 'Redmond'}
{ 'MSFT', 1, 'Charger', 2, 'Redmond'}
{ 'MSFT', 2, 'Mice', 2, 'Redmond'}
{ 'MSFT', 2, 'Keyboard', 1, 'Redmond'}
{ 'Company1', 4, 'Laptop', 20, 'Seattle'}
{ 'Company1', 4, 'Charger', 3, 'Seattle'}
{ 'Company1', 5, 'Chair', 4, 'Seattle'}
{ 'Company1', 5, 'Lamp', 2, 'Seattle'}
{ 'Company2', 4, null, null, 'Bellevue'}
{ 'Company3', null, null, null, 'Kirkland'}
```

### <a name="same-root-as-unroll-array"></a>与卷外卷阵列相同的根

| 展开者 | 展开根 | 投影 |
| --------- | ----------- | ---------- |
| 货物.订单 | 货物.订单 | name <br> 货物.订单.发货.订单项目.项目名称 <br> 货物.客户 <br> location |

#### <a name="output"></a>输出

```
{ 'MSFT', ['Laptop','Charger'], ['government','distributer','retail'], 'Redmond'}
{ 'MSFT', ['Mice', 'Keyboard'], ['government','distributer','retail'], 'Redmond'}
{ 'Company1', ['Laptop','Charger'], ['store', 'store2'], 'Seattle'}
{ 'Company1', ['Chair', 'Lamp'], ['store', 'store2'], 'Seattle'}
{ 'Company2', null, ['Bank'], 'Bellevue'}
```

### <a name="unroll-root-with-complex-array"></a>使用复杂数组展开根

| 展开者 | 展开根 | 投影 |
| --------- | ----------- | ---------- |
| 货物.订单.发货.订单项目 | 货物.订单 |name <br> 订单 Id = 货物.订单.订单Id <br> 项目名称 = 货物.订单.发货.订单项目.项目名称 <br> 物料数量 = 货物.订单.发货.订单项目.项目数量 <br> 位置 = 位置 |

#### <a name="output"></a>输出

```
{ 'MSFT', 1, 'Laptop', 20, 'Redmond'}
{ 'MSFT', 1, 'Charger', 2, 'Redmond'}
{ 'MSFT', 2, 'Mice', 2, 'Redmond'}
{ 'MSFT', 2, 'Keyboard', 1, 'Redmond'}
{ 'Company1', 4, 'Laptop', 20, 'Seattle'}
{ 'Company1', 4, 'Charger', 3, 'Seattle'}
{ 'Company1', 5, 'Chair', 4, 'Seattle'}
{ 'Company1', 5, 'Lamp', 2, 'Seattle'}
{ 'Company2', 4, null, null, 'Bellevue'}
```

## <a name="data-flow-script"></a>数据流脚本

### <a name="syntax"></a>语法

```
<incomingStream>
foldDown(unroll(<unroll cols>),
    mapColumn(
        name,
        each(<array>(type == '<arrayDataType>')),
        each(<array>, match(true())),
        location
    )) ~> <transformationName>
```

### <a name="example"></a>示例

```
source foldDown(unroll(goods.orders.shipped.orderItems, goods.orders),
    mapColumn(
        name,
        orderId = goods.orders.orderId,
        itemName = goods.orders.shipped.orderItems.itemName,
        itemQty = goods.orders.shipped.orderItems.itemQty,
        location = location
    ),
    skipDuplicateMapInputs: false,
    skipDuplicateMapOutputs: false) 
```    

## <a name="next-steps"></a>后续步骤

* 使用[数据透视转换](data-flow-pivot.md)将行透视到列。
* 使用["取消透视"转换](data-flow-unpivot.md)将列透视到行。
