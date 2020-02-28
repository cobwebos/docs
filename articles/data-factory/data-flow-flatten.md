---
title: 映射数据流平展转换
description: Azure 数据工厂映射数据流平展转换
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: 435d2469be8eb572caa02e381d84ae4e9ac32f4b
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77674839"
---
# <a name="azure-data-factory-flatten-transformation"></a>Azure 数据工厂平展转换

平展转换可用于将层次结构内的数组值透视到新行，实质上是非规范化数据。

![转换工具箱](media/data-flow/flatten5.png "转换工具箱")

![平展转换1](media/data-flow/flatten7.png "平展转换1")

## <a name="unroll-by"></a>展开

首先，选择要展开并透视的数组列。

![平展转换设置](media/data-flow/flatten1.png "平展转换设置")

## <a name="unroll-root"></a>展开根

默认情况下，ADF 会将结构平展到您在上面选择的展开数组。 或者，您可以选择层次结构的其他部分，以展开到。

## <a name="input-columns"></a>输入列

最后，根据传入字段以及您 unrolled 的规范化列，选择新结构的投影。

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
source(output(
        name as string,
        location as string,
        satellites as string[],
        goods as (trade as boolean, customers as string[], orders as (orderId as string, orderTotal as double, shipped as (orderItems as (itemName as string, itemQty as string)[]))[])
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source2
source2 foldDown(unroll(goods.orders.shipped.orderItems),
    mapColumn(
        name,
        each(goods.orders, match(type == 'integer')),
        each(goods.orders.shipped.orderItems, match(true())),
        location
    )) ~> Flatten1
Flatten1 sink(allowSchemaDrift: true,
    validateSchema: false,
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> sink1
```    

## <a name="next-steps"></a>后续步骤

* 使用[透视转换](data-flow-pivot.md)将行透视到列。
* 使用[逆透视转换](data-flow-unpivot.md)将列透视到行。
