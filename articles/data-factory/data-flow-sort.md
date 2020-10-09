---
title: 映射数据流中的排序转换
description: Azure 数据工厂映射数据排序转换
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/14/2020
ms.openlocfilehash: 26852ec77194714c8236856b7cb496170bf0d777
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "81606323"
---
# <a name="sort-transformation-in-mapping-data-flow"></a>映射数据流中的排序转换

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

排序转换允许对当前数据流中的传入行进行排序。 您可以选择各个列并按升序或降序对它们进行排序。

> [!NOTE]
> 在将数据分布到多个节点和分区的 spark 群集上执行映射数据流。 如果选择在后续转换中对数据进行重新分区，则可能会由于重新组织数据而丢失排序。

## <a name="configuration"></a>Configuration

![排序设置](media/data-flow/sort.png "排序")

不**区分大小写：** 是否希望在对字符串或文本字段进行排序时忽略大小写

**仅在分区中排序：** 当数据流在 spark 上运行时，每个数据流都划分为多个分区。 此设置仅对传入分区内的数据进行排序，而不是对整个数据流进行排序。 

**排序条件：** 选择要排序的列以及排序发生的顺序。 顺序决定排序优先级。 选择 null 是否将显示在数据流的开头或结尾。

### <a name="computed-columns"></a>计算列

若要在应用排序之前修改或提取列值，请将鼠标悬停在该列上，然后选择 "计算列"。 这将打开 "表达式生成器" 来创建用于排序操作的表达式，而不是使用列值。

## <a name="data-flow-script"></a>数据流脚本

### <a name="syntax"></a>语法

```
<incomingStream>
    sort(
        desc(<sortColumn1>, { true | false }),
        asc(<sortColumn2>, { true | false }),
        ...
    ) ~> <sortTransformationName<>
```

### <a name="example"></a>示例

![排序设置](media/data-flow/sort.png "排序")

上述排序配置的数据流脚本位于下面的代码段中。

```
BasketballStats sort(desc(PTS, true),
    asc(Age, true)) ~> Sort1
```

## <a name="next-steps"></a>后续步骤

排序后，您可能希望使用 [聚合转换](data-flow-aggregate.md)
