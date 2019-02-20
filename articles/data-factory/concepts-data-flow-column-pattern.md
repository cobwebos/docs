---
title: Azure 数据工厂映射数据流列模式
description: Azure 数据工厂映射数据流列模式用于创建通用化模板模式，以便转换数据流中的字段，不需考虑基础架构元数据。
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: dd397259ebcd6afe34a47a6f853d826a845a1244
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2019
ms.locfileid: "56212331"
---
# <a name="azure-data-factory-mapping-data-flow-concepts"></a>Azure 数据工厂映射数据流概念

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

多个 Azure 数据工厂数据流转换支持“列模式”理念，因此你可以根据模式而不是硬编码的列名称来创建模板列。 可以在表达式生成器中使用此功能来定义模式，以便匹配需要转换的列，而不需确切且具体的字段名称。 当传入的源字段经常更改时，模式很有用，尤其是在文本文件或 NoSQL 数据库中更改列的情况下。 有时这称为“架构偏差”。

![列模式](media/data-flow/columnpattern2.png "列模式")

列模式用于处理架构偏差方案和传给方案。 它适用于你无法完全知道每个列名的情况。 可以在列名和列数据类型上进行模式匹配，然后生成一个用于转换的表达式，以便针对数据流中与 `name` & `type` 模式匹配的任何字段执行该操作。

将表达式添加到接受多种模式的转换时，请选择“添加列模式”。 列模式允许架构偏差列匹配模式。

生成模板列模式时，请在表达式中使用 `$$` 来表示对输入数据流中每个已匹配字段的引用。

如果选择使用某个表达式生成器正则表达式函数，则可随后使用 $1、$2、$3 ... 来引用正则表达式中匹配的子模式。

列模式方案的一个示例是对一系列传入字段使用 SUM。 聚合 SUM 计算位于“聚合”转换中。 然后，可以对与“integer”匹配的字段类型的每个匹配项执行 SUM 计算，再使用 $$ 来引用表达式中的每个匹配项。
