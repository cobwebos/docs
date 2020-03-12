---
title: 在映射数据流中平展转换
description: 使用平展转换非规范化分层数据
author: kromerm
ms.author: makromer
ms.review: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 74f6df1fbc749a5ec015afb954ca6b12cbe0f18f
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2020
ms.locfileid: "79086960"
---
# <a name="flatten-transformation-in-mapping-data-flow"></a>在映射数据流中平展转换

使用平展转换在层次结构（如 JSON）中获取数组值，并将其展开到各个行。 此过程称为非规范化。

## <a name="configuration"></a>配置

平展转换包含以下配置设置

![平展设置](media/data-flow/flatten1.png "平展设置")

### <a name="unroll-by"></a>展开

选择要展开的数组。 每个数组中的每个项的输出数据都占一行。 如果输入行中的展开数组为 null 或为空，则会有一个输出行的 unrolled 值为 null。

### <a name="unroll-root"></a>展开根

默认情况下，平展转换 unrolls 将数组置于其中存在的层次结构的顶部。 你可以选择将数组选择为展开根。 展开根必须是复杂对象的数组，这些对象要么为，要么包含展开 by 数组。 如果选择了展开根，则输出数据将在展开根中的每个项中至少包含一行。 如果输入行没有展开根中的任何项，则会将其从输出数据中删除。 选择展开根将始终输出小于或等于默认行为的行数。

### <a name="flatten-mapping"></a>平展映射

类似于 "选择转换"，从传入字段和非规范化数组中选择新结构的投影。 如果映射了非规范化数组，则输出列的数据类型将与数组的数据类型相同。 如果展开 by 数组是包含子的复杂对象数组，则映射该 subarry 的项将输出一个数组。

请参阅检查选项卡和数据预览，验证映射输出。

## <a name="examples"></a>示例

有关平展转换的以下示例，请参阅以下 JSON 对象

``` json
[{
  "name":"MSFT","location":"Redmond", "satellites": ["Bay Area", "Shanghai"],
  "goods": {
    "trade":true, "customers":["government", "distributer", "retail"],
    "orders":[
        {"orderId":1,"orderTotal":123.34,"shipped":{"orderItems":[{"itemName":"Laptop","itemQty":20},{"itemName":"Charger","itemQty":2}]}},
        {"orderId":2,"orderTotal":323.34,"shipped":{"orderItems":[{"itemName":"Mice","itemQty":2},{"itemName":"Keyboard","itemQty":1}]}}
    ]}},
{"name":"Company1","location":"Seattle", "satellites": ["New York"],
  "goods":{"trade":false, "customers":["store1", "store2"],
  "orders":[
      {"orderId":4,"orderTotal":123.34,"shipped":{"orderItems":[{"itemName":"Laptop","itemQty":20},{"itemName":"Charger","itemQty":3}]}},
      {"orderId":5,"orderTotal":343.24,"shipped":{"orderItems":[{"itemName":"Chair","itemQty":4},{"itemName":"Lamp","itemQty":2}]}}
    ]}},
{"name": "Company2", "location": "Bellevue",
  "goods": {"trade": true, "customers":["Bank"], "orders": [{"orderId": 4, "orderTotal": 123.34}]}},
{"name": "Company3", "location": "Kirkland"}]
```

### <a name="no-unroll-root-with-string-array"></a>没有带字符串数组的展开根

| 展开 | 展开根 | 投影 |
| --------- | ----------- | ---------- |
| 商品。客户 | 无 | name <br> customer = 客户 |

#### <a name="output"></a>输出

```
{ 'MSFT', 'government'},
{ 'MSFT', 'distributer'},
{ 'MSFT', 'retail'},
{ 'Company1', 'store'},
{ 'Company1', 'store2'},
{ 'Company2', 'Bank'},
{ 'Company3', null}
```

### <a name="no-unroll-root-with-complex-array"></a>没有包含复杂数组的展开根

| 展开 | 展开根 | 投影 |
| --------- | ----------- | ---------- |
| 发货。 orderItems | 无 | name <br> 订单 Id = 发货情况订单 Id <br> 命令% = orderItems。 <br> itemQty = orderItems. itemQty <br> 位置 = 位置 |

#### <a name="output"></a>输出

```
{ 'MSFT', 1, 'Laptop', 20, 'Redmond'},
{ 'MSFT', 1, 'Charger', 2, 'Redmond'},
{ 'MSFT', 2, 'Mice', 2, 'Redmond'},
{ 'MSFT', 2, 'Keyboard', 1, 'Redmond'},
{ 'Company1', 4, 'Laptop', 20, 'Seattle'},
{ 'Company1', 4, 'Charger', 3, 'Seattle'},
{ 'Company1', 5, 'Chair', 4, 'Seattle'},
{ 'Company1', 5, 'Lamp', 2, 'Seattle'},
{ 'Company2', 4, null, null, 'Bellevue'},
{ 'Company3', null, null, null, 'Kirkland'}
```

### <a name="same-root-as-unroll-array"></a>与展开数组相同的根

| 展开 | 展开根 | 投影 |
| --------- | ----------- | ---------- |
| 货物订单 | 货物订单 | name <br> orderItems..。 <br> 商品。客户 <br> location |

#### <a name="output"></a>输出

```
{ 'MSFT', ['Laptop','Charger'], ['government','distributer','retail'], 'Redmond'},
{ 'MSFT', ['Mice', 'Keyboard'], ['government','distributer','retail'], 'Redmond'},
{ 'Company1', ['Laptop','Charger'], ['store', 'store2'], 'Seattle'},
{ 'Company1', ['Chair', 'Lamp'], ['store', 'store2'], 'Seattle'},
{ 'Company2', null, ['Bank'], 'Bellevue'}
```

### <a name="unroll-root-with-complex-array"></a>带有复杂数组的展开根

| 展开 | 展开根 | 投影 |
| --------- | ----------- | ---------- |
| 发货。 orderItem | 货物订单 |name <br> 订单 Id = 发货情况订单 Id <br> 命令% = orderItems。 <br> itemQty = orderItems. itemQty <br> 位置 = 位置 |

#### <a name="output"></a>输出

```
{ 'MSFT', 1, 'Laptop', 20, 'Redmond'},
{ 'MSFT', 1, 'Charger', 2, 'Redmond'},
{ 'MSFT', 2, 'Mice', 2, 'Redmond'},
{ 'MSFT', 2, 'Keyboard', 1, 'Redmond'},
{ 'Company1', 4, 'Laptop', 20, 'Seattle'},
{ 'Company1', 4, 'Charger', 3, 'Seattle'},
{ 'Company1', 5, 'Chair', 4, 'Seattle'},
{ 'Company1', 5, 'Lamp', 2, 'Seattle'},
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

* 使用[透视转换](data-flow-pivot.md)将行透视到列。
* 使用[逆透视转换](data-flow-unpivot.md)将列透视到行。
