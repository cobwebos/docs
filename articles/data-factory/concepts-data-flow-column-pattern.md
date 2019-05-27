---
title: Azure 数据工厂映射数据流列模式
description: 了解如何在映射的数据流中使用 Azure 数据工厂列模式来创建用于转换数据流中的数据而不考虑基础架构元数据字段的通用的模板模式
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 5131329f6675bc86374f5a5c081e0aaa7d36c0fe
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/23/2019
ms.locfileid: "66155238"
---
# <a name="azure-data-factory-mapping-data-flows-column-patterns"></a>Azure 数据工厂映射数据流列模式

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

多个 Azure 数据工厂数据流转换支持“列模式”理念，因此你可以根据模式而不是硬编码的列名称来创建模板列。 可以在表达式生成器中使用此功能来定义模式，以便匹配需要转换的列，而不需确切且具体的字段名称。 模式是传入的源字段经常更改，尤其对于更改文本的文件或 NoSQL 数据库中的列的情况下很有用。 这种情况有时称为"架构偏差"。

![列模式](media/data-flow/columnpattern2.png "列模式")

列模式用于处理架构偏差方案和传给方案。 它适用于你无法完全知道每个列名的情况。 可以在列名和列数据类型上进行模式匹配，然后生成一个用于转换的表达式，以便针对数据流中与 `name` & `type` 模式匹配的任何字段执行该操作。

将表达式添加到接受多种模式的转换时，请选择“添加列模式”。 列模式允许架构偏差列匹配模式。

生成模板列模式时，请在表达式中使用 `$$` 来表示对输入数据流中每个已匹配字段的引用。

如果选择使用某个表达式生成器正则表达式函数，则可随后使用 $1、$2、$3 ... 来引用正则表达式中匹配的子模式。

列模式方案的一个示例是对一系列传入字段使用 SUM。 聚合 SUM 计算位于“聚合”转换中。 然后，可以对与“integer”匹配的字段类型的每个匹配项执行 SUM 计算，再使用 $$ 来引用表达式中的每个匹配项。
