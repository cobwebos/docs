---
title: 映射数据流的排序转换
description: Azure 数据工厂映射数据排序转换
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/14/2020
ms.openlocfilehash: 26852ec77194714c8236856b7cb496170bf0d777
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606323"
---
# <a name="sort-transformation-in-mapping-data-flow"></a>映射数据流的排序转换

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

排序转换允许您对当前数据流上的传入行进行排序。 您可以选择单个列，然后按升序或降序排序。

> [!NOTE]
> 映射数据流在跨多个节点和分区分布数据的 Spark 群集上执行。 如果选择在后续转换中重新分区数据，则可能会由于重新洗牌而丢失排序。

## <a name="configuration"></a>配置

![排序设置](media/data-flow/sort.png "排序")

**不区分大小写：** 在排序字符串或文本字段时是否希望忽略大小写

**仅在分区内排序：** 当数据流在火花上运行时，每个数据流被划分为分区。 此设置仅对传入分区中的数据进行排序，而不是对整个数据流进行排序。 

**排序条件：** 选择要按排序的列以及排序顺序。 顺序确定排序优先级。 选择空值是否出现在数据流的开头或结尾。

### <a name="computed-columns"></a>计算列

要在应用排序之前修改或提取列值，请将鼠标悬停在列上并选择"计算列"。 这将打开表达式生成器以创建排序操作的表达式，而不是使用列值。

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

排序后，您可能需要使用[聚合转换](data-flow-aggregate.md)
