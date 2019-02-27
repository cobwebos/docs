---
title: Azure 数据工厂映射数据流代理键转换
description: Azure 数据工厂映射数据流代理键转换
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 62e9879f6bc75f042669ecb931ca7459d1317cc7
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2019
ms.locfileid: "56271015"
---
# <a name="azure-data-factory-mapping-data-flow-surrogate-key-transformation"></a>Azure 数据工厂映射数据流代理键转换

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

使用代理键转换将递增的非业务任意键值添加到数据流行集。 这对于在星型架构分析数据模型中设计维度表非常有用，在该模型中维度表中的每个成员都需要拥有一个非业务的唯一键（这是 Kimball DW 方法的一部分）。

![代理键转换](media/data-flow/surrogate.png "Surrogate Key Transformation")

“键列”是将赋予新代理键列的名称。

"起始值"是增量值的起始点。
