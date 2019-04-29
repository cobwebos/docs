---
title: Azure 数据工厂映射数据流的“检查”和“数据预览”
description: Azure 数据工厂映射数据流有一个窗格，该窗格可以显示数据流元数据（“检查”）和调试模式下的数据预览（“数据预览”）
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 47cde50e0874f0f73523925b6d1b2f8ee4abaea9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61284009"
---
# <a name="azure-data-factory-mapping-data-flow-transformation-inspect-tab"></a>Azure 数据工厂映射数据流的“转换检查”选项卡

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![“检查”窗格](media/data-flow/agg3.png "“检查”窗格")

可以通过“检查”窗格了解正在转换的数据流的元数据。 将可以看到列计数、更改的列、添加的列、数据类型、列排序以及列引用。 “检查”视图是针对元数据的只读视图。 不需启用“调试”模式即可在“检查”窗格中查看元数据。

以转换方式更改数据的形状时，可以通过“检查”窗格查看元数据更改流。 如果源转换中没有定义的架构，则元数据将在“检查”窗格中不可见。 在“架构偏差”方案中，缺少元数据是很常见的。

![数据预览](media/data-flow/datapreview.png "数据预览")

“数据预览”是一个窗格，可以在转换数据时以只读方式对其进行查看。 可以通过查看转换和表达式的输出来验证数据流。 必须将“调试”模式切换到开才能查看数据预览结果。 单击数据预览网格中的列时，会在右侧看到后续面板。 该弹出面板会显示所选的每个列的配置文件统计信息。

![数据预览图表](media/data-flow/chart.png "数据预览图表")

