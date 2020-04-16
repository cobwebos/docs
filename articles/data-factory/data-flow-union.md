---
title: 映射数据流联合转换
description: Azure 数据工厂映射数据流 新分支转换
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 02/12/2019
ms.openlocfilehash: 3bdf443a052ba8884a361774154f4d68cc180dfb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417821"
---
# <a name="azure-data-factory-mapping-data-flow-union-transformation"></a>Azure 数据工厂映射数据流联合转换

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Union 会将多个数据流组合成一个数据流，而这些流的 SQL Union 会作为 Union 转换的新输出。 每个输入流中的所有架构都将在数据流内部合并，而无需具有联接键。

您可以通过选择每个配置行旁边的"+"图标（包括源数据以及数据流中现有转换的流）来组合设置表中的 n 个流。

![联合转换](media/data-flow/union.png "Union")

在这种情况下，可以合并来自多个源的不同元数据（在此示例中为三个不同的源文件），并将它们合并到单个流中：

![联合转换概述](media/data-flow/union111.png "联盟 1")

为此，通过在"联合设置"中添加其他行，包括要添加的所有源。 不需要通用查找或联接键：

![联合转换设置](media/data-flow/unionsettings.png "联合设置")

如果在 Union 之后设置 Select 转换，则可以重命名未从无标头源命名的重叠字段或字段。 单击"检查"以查看此示例中来自三个不同来源的 132 个总列的组合元数据：

![联合转型最终](media/data-flow/union333.png "联盟 3")

## <a name="name-and-position"></a>名称和位置

当您选择"按名称联合"时，每个列值将从每个源放入相应的列中，并带有一个新的串联元数据架构。

如果选择"按位置合并"，则每个列值将从每个对应源下降到原始位置，从而产生新的合并数据流，其中每个源的数据将添加到同一流：

![联合输出](media/data-flow/unionoutput.png "联合输出")

## <a name="next-steps"></a>后续步骤

探索类似的转换，包括[联接](data-flow-join.md)和[存在](data-flow-exists.md)。
