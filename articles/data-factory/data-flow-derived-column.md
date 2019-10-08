---
title: 映射数据流中的派生列转换-Azure 数据工厂 |Microsoft Docs
description: 了解如何在 Azure 数据工厂中大规模转换数据以及映射数据流派生列转换。
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: aacd6f1799f1813e168bd04e78f18cf60ad5243f
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2019
ms.locfileid: "72026849"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>映射数据流中的派生列转换

使用派生列转换在数据流中生成新列或修改现有字段。

## <a name="derived-column-settings"></a>派生列设置

若要覆盖现有列，请通过列下拉列表选择它。 否则，使用 "列选择" 字段作为文本框，然后键入新列的名称。 若要生成派生列的表达式，请单击 "输入表达式" 框以打开 "数据流[表达式生成器](concepts-data-flow-expression-builder.md)"。

派生![列设置](media/data-flow/dc1.png "派生列设置")

若要添加其他派生列，请将鼠标悬停在现有的派生列上，并单击 "+"。 然后，选择 "添加列" 或 "添加列模式"。 如果列名称是来自源的变量，则列模式可能会很方便。 有关详细信息，请参阅[列模式](concepts-data-flow-column-pattern.md)。

![新建派生列选定]内容(media/data-flow/columnpattern.png "新建派生列")

## <a name="next-steps"></a>后续步骤

- 了解有关[映射数据流表达式语言](data-flow-expression-functions.md)的详细信息。
