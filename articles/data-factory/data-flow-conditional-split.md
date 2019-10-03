---
title: Azure 数据工厂映射数据流有条件拆分转换
description: Azure 数据工厂数据流有条件拆分转换
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: dd51cc2d5f95598154b76b5da8e3fc9e4801100d
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71104950"
---
# <a name="mapping-data-flow-conditional-split-transformation"></a>映射数据流有条件拆分转换

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![有条件拆分工具箱](media/data-flow/conditionalsplit2.png "有条件拆分工具箱")

有条件拆分转换可以将数据行路由到不同的流，具体取决于数据的内容。 有条件拆分转换的实现类似于编程语言中的 CASE 决策结构。 转换计算表达式，并根据结果将数据行定向到指定的流。 此转换还提供默认输出，因此，如果某行与任何表达式都不匹配，它会被定向到默认输出。

![有条件拆分](media/data-flow/conditionalsplit1.png "有条件拆分选项")

## <a name="multiple-paths"></a>多个路径

若要添加附加条件，请在底部配置窗格中选择“添加流”，并单击“表达式生成器”文本框来生成表达式。

![有条件拆分（多个）](media/data-flow/conditionalsplit3.png "有条件拆分（多个）")

## <a name="next-steps"></a>后续步骤

用于有条件拆分的常见数据流转换：[联接转换](data-flow-join.md)，[查找转换](data-flow-lookup.md)，[选择转换](data-flow-select.md)
