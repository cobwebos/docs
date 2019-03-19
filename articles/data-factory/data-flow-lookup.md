---
title: Azure 数据工厂映射数据流查找转换
description: Azure 数据工厂映射数据流查找转换
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: b156917a9987b023a9bf94e51c0cc14aebb133c7
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/23/2019
ms.locfileid: "56738379"
---
# <a name="azure-data-factory-mapping-data-flow-lookup-transformation"></a>Azure 数据工厂映射数据流查找转换

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

使用“查找”将其他源的参考数据添加到数据流中。 查找转换需要一个定义的源，它指向你的引用表并匹配关键字段。

![查找转换](media/data-flow/lookup1.png "查找")

选择要在传入流字段和参考源字段之间匹配的关键字段。 必须先在数据流设计画布上创建一个新源，以在右侧用于查找。

找到匹配项后，参考源中生成的行和列将添加到数据流中。 可以在数据流的末尾选择要包含在接收器中的感兴趣的字段。
