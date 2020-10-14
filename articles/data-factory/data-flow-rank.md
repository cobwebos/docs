---
title: 映射数据流中的排名转换
description: 如何使用 Azure 数据工厂的映射数据流排名转换生成排名列
author: djpmsft
ms.author: daperlov
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/05/2020
ms.openlocfilehash: 8584d1b64191cc5258c6eeeef9ae4125bf1a2c65
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92044728"
---
# <a name="rank-transformation-in-mapping-data-flow"></a>映射数据流中的排名转换 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

根据用户指定的排序条件，使用排名转换生成排序排名。 

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4GGJo]

## <a name="configuration"></a>配置

![排名设置](media/data-flow/rank-configuration.png "排名设置")

不**区分大小写：** 如果排序列的类型为 string，则会将 case 分解为排名。 

**密集：** 如果启用，则排名列将为密集排名。 每个排名计数都是连续数字，并且在绑定后不会跳过排名值。

**排名列：** 生成的排名列的名称。 此列的类型为 long。

**排序条件：** 选择要排序的列以及排序发生的顺序。 顺序决定排序优先级。

上述配置将使用传入的篮球数据，并创建一个名为 "pointsRanking" 的排名列。 具有列 *pt* 最大值的行的 *pointsRanking* 值为1。

## <a name="data-flow-script"></a>数据流脚本

### <a name="syntax"></a>语法

```
<incomingStream>
    rank(
        desc(<sortColumn1>),
        asc(<sortColumn2>),
        ...,
        caseInsensitive: { true | false }
        dense: { true | false }
        output(<rankColumn> as long)
    ) ~> <sortTransformationName<>
```

### <a name="example"></a>示例

![排名设置](media/data-flow/rank-configuration.png "排名设置")

以下代码片段中提供了上述排名配置的数据流脚本。

```
PruneColumns
    rank(
        desc(PTS, true),
        caseInsensitive: false,
        output(pointsRanking as long),
        dense: false
    ) ~> RankByPoints
```

## <a name="next-steps"></a>后续步骤

使用 [筛选器转换](data-flow-filter.md)基于排名值筛选行。
