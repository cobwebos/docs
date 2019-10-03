---
title: 派生列转换映射数据在流中的 Azure 数据工厂 |Microsoft Docs
description: 了解如何在 Azure 数据工厂中使用映射数据流派生列转换的大规模转换数据。
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: 941c629fd8359edc7fc1cf364a6735314044d95e
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2019
ms.locfileid: "67312195"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>在映射数据流中的派生的列转换

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

使用派生列转换在数据流中生成新列或修改现有字段。

## <a name="derived-column-settings"></a>派生的列设置

若要覆盖现有列，请通过列下拉列表中选择它。 否则，使用列选择字段作为文本框中，键入新列的名称。 若要生成派生的列的表达式，单击输入表达式框以打开[流数据的表达式生成器](concepts-data-flow-expression-builder.md)。

![派生列设置](media/data-flow/dc1.png "派生的列设置")

若要添加其他派生的列，悬停在某个现有派生列，然后单击 +。 然后，选择添加列或添加列模式。 列模式可能会派上用场如果列名称将从你的源的变量。 有关详细信息，请参阅[列模式](concepts-data-flow-column-pattern.md)。

![新建派生列选择](media/data-flow/columnpattern.png "新建派生列选择")

## <a name="next-steps"></a>后续步骤

- 详细了解如何[映射数据流表达式语言](data-flow-expression-functions.md)。
