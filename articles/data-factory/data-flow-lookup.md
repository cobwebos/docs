---
title: Azure 数据工厂映射数据流查找转换
description: Azure 数据工厂映射数据流查找转换
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: 28c8f2b0005641934f7fcd9549f1cf004b206d80
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2019
ms.locfileid: "56270889"
---
# <a name="azure-data-factory-mapping-data-flow-lookup-transformation"></a>Azure 数据工厂映射数据流查找转换

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

使用“查找”将其他源的参考数据添加到数据流中。 查找转换需要一个定义的源，它指向你的引用表并匹配关键字段。

![查找转换](media/data-flow/lookup1.png "查找")

选择要在传入流字段和参考源字段之间匹配的关键字段。 必须先在数据流设计画布上创建一个新源，以在右侧用于查找。

找到匹配项后，参考源中生成的行和列将添加到数据流中。 可以在数据流的末尾选择要包含在接收器中的感兴趣的字段。
