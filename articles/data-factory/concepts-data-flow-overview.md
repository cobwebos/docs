---
title: Azure 数据工厂映射数据流概述
description: Azure 数据工厂中的映射数据流的概述说明
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 6f4c124c59584c8538d85ac61650661ae559a77b
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70123943"
---
# <a name="what-are-mapping-data-flows"></a>什么是映射数据流？

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

在 Azure 数据工厂中, 映射数据流是直观设计的数据转换。 数据流允许数据工程师开发图形数据转换逻辑, 而无需编写代码。 生成的数据流是使用横向扩展的 Azure Databricks 群集作为活动在 Azure 数据工厂管道内执行的。

Azure 数据工厂数据流的用途是在无需编码的情况下提供完全可视化的体验。 数据流将在你自己的执行群集上执行以进行横向扩展的数据处理。 Azure 数据工厂处理数据流作业的所有代码转换、路径优化以及执行。

首先在调试模式下创建数据流，以便能够以交互方式验证你的转换逻辑。 接下来，向管道中添加一个数据流活动以在管道调试中执行并测试你的数据流，或者在管道中使用“立即触发”来测试管道活动中的数据流。

然后，使用执行数据流活动的 Azure 数据工厂管道来计划并监视数据流活动。

数据流设计界面上的“调试模式”切换开关允许以交互方式构建数据转换。 调试模式为数据流构造提供数据准备和数据预览环境。

## <a name="begin-building-your-data-flow-logical-graph"></a>开始生成数据流逻辑图

使用 "工厂资源" 下的 "+" 符号, 开始生成数据流。

![新建]数据流(media/data-flow/newdataflow2.png "新建")数据流

首先配置源转换, 然后使用 + 符号将数据转换添加到每个后续步骤。 构建逻辑图时, 可以使用 "显示关系图" 和 "隐藏关系图" 按钮在关系图和配置模式之间进行切换。

![显示关系图](media/data-flow/showg.png "显示关系图")

## <a name="configure-transformation-logic"></a>配置转换逻辑

![隐藏关系图](media/data-flow/hideg.png "隐藏关系图")

隐藏您的图形将允许您在 "转换" 节点侧面导航。

![导航](media/data-flow/showhide.png "导航")

## <a name="next-steps"></a>后续步骤

* [以源转换开头](data-flow-source.md)
