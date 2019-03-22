---
title: Azure 数据工厂映射数据流“新建分支”转换
description: Azure 数据工厂映射数据流“新建分支”转换
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: af2225d749283c7124f89d5a7cd735b2f6bfd121
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2019
ms.locfileid: "57792610"
---
# <a name="mapping-data-flow-union-transformation"></a>映射数据流联合转换

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Union 会将多个数据流组合成一个数据流，而这些流的 SQL Union 会作为 Union 转换的新输出。 每个输入流中的架构的所有将结合使用内部数据的流，而无需具有联接键。

可以通过选择已配置的每一行，包括在数据流中的源数据以及从现有转换的流旁边的"+"图标来组合 n 数设置表中的流。

![联合转换](media/data-flow/union.png "联合")

在这种情况下，可以组合来自多个源 （在此示例中，三个不同的源文件） 的不同元数据，并将它们合并成一个流：

![联合转换概述](media/data-flow/union111.png "联合 1")

若要实现此目的，请包括想要添加的所有源联合设置中添加其他行。 还有一个公共的查找或联接键不需要：

![联合转换设置](media/data-flow/unionsettings.png "联合设置")

如果将选择转换后在联合，你将能够重命名重叠字段或已从 headerless 源未命名的字段。 单击"检查"以查看具有 132 总列在此示例中三个不同来源的合并元数据：

![联合转换最终](media/data-flow/union333.png "联合 3")

## <a name="name-and-position"></a>名称和位置

当选择"按名称 union"时，每个列的值将放入每个源，使用新的连接的元数据架构中的对应列。

如果选择"按位置 union"，每个列的值将放入每个相应的源，导致其中每个源中的数据添加到同一个流的数据的新组合流中的原始位置：

![Union 的输出](media/data-flow/unionoutput.png "Union 的输出")

## <a name="next-steps"></a>后续步骤

浏览相似的转换，包括[加入](data-flow-join.md)并[Exists](data-flow-exists.md)。
