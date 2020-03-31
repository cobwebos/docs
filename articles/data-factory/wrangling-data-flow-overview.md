---
title: 在 Azure 数据工厂中旋转数据流
description: Azure 数据工厂中争用数据流的概述
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 8f91ed926c733b211443805722b6817b1ce005b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77048363"
---
# <a name="what-are-wrangling-data-flows"></a>什么是在争论数据流？

组织需要进行数据准备和争论，以便准确分析数据，这些数据每天都在增长。 数据准备是必需的，以便组织可以在各种业务流程中使用数据并缩短价值时间。

通过 Azure 数据工厂中旋转数据流，您可以迭代云规模进行无代码数据准备。 旋转数据流与[联机电源查询](https://docs.microsoft.com/power-query/)集成，并使电源查询 M 函数可供数据工厂用户使用。

旋转数据流将电源查询联机混搭编辑器生成的 M 转换为用于云缩放执行的火花代码。

对数据工程师或"公民数据集成商"来说，争鸣数据流尤其有用。

## <a name="use-cases"></a>用例

### <a name="fast-interactive-data-exploration-and-preparation"></a>快速交互式数据探索和准备

多个数据工程师和公民数据集成商可以在云规模上以交互方式探索和准备数据集。 随着数据湖中数据量、多样性和速度的增加，用户需要一种有效的方法来探索和准备数据集。 例如，您可能需要创建一个数据集，该数据集"自 2017 年以来已为新客户提供了所有客户人口统计信息"。 您没有映射到已知目标。 在将数据集发布到湖中之前，您正在浏览、争用和准备数据集以满足要求。 争鸣数据流通常用于不太正式的分析方案。 预置数据集可用于在下游执行转换和机器学习操作。

### <a name="code-free-agile-data-preparation"></a>无代码敏捷数据准备

公民数据集成商花费超过 60% 的时间来查找和准备数据。 他们希望以无代码的方式做到这一点，以提高运营效率。 允许公民数据集成商使用已知工具（如 Power Query Online）以可扩展的方式丰富、塑造和发布数据，从而大大提高了工作效率。 在 Azure 数据工厂中，Wrangrang 数据流使熟悉的 Power 查询联机混搭编辑器能够允许公民数据集成商快速修复错误、标准化数据并生成高质量的数据以支持业务决策。

### <a name="data-validation"></a>数据验证

以无代码方式直观地扫描数据，以删除任何异常值、异常值，并将其与形状保持一致，以便快速分析。

## <a name="supported-sources"></a>支持的来源

| 连接器 | 数据格式 | 身份验证类型 |
| -- | -- | --|
| [Azure Blob 存储](connector-azure-blob-storage.md) | CSV， 镶木地板 | 帐户密钥 |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | CSV | Service Principal |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | CSV， 镶木地板 | 帐户密钥，服务主体 |
| [Azure SQL 数据库](connector-azure-sql-database.md) | - | SQL 身份验证 |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) | - | SQL 身份验证 |

## <a name="the-mashup-editor"></a>混搭编辑器

创建争鸣的数据流时，所有源数据集都将变为数据集查询，并放置在**ADFResource**文件夹中。 默认情况下，UserQuery 将指向第一个数据集查询。 所有转换都应在 UserQuery 上完成，因为不支持对数据集查询的更改，也不会持久化更改。 当前不支持重命名、添加和删除查询。

![争吵](media/wrangling-data-flow/editor.png)

尽管在创作期间可用，但目前并非所有 Power Query M 函数都支持用于数据争用。 构建争用数据流时，如果不支持函数，系统将提示您出现以下错误消息：

`The wrangling data flow is invalid. Expression.Error: The transformation logic isn't supported. Please try a simpler expression`

有关支持的转换的详细信息，请参阅[争用数据流函数](wrangling-data-flow-functions.md)。

当前，争鸣的数据流仅支持写入一个接收器。

## <a name="next-steps"></a>后续步骤

了解如何[创建争鸣的数据流](wrangling-data-flow-tutorial.md)。