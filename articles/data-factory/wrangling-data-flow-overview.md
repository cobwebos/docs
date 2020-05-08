---
title: Azure 数据工厂中的整理数据流
description: Azure 数据工厂中的整理数据流概述
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 3b8632eaf7662d705235adaad7cd03d2e7e4904b
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82626865"
---
# <a name="what-are-wrangling-data-flows"></a>什么是整理数据流？

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

组织需要进行数据准备和整理，以便准确分析每天持续增长的复杂数据。 需要进行数据准备，以便组织可以使用各种业务流程中的数据并缩短价值。

通过 Azure 数据工厂中的整理数据流，可以在云规模上以迭代方式进行无代码的数据准备。 整理数据流与[Power Query Online](https://docs.microsoft.com/power-query/)集成，使 Power Query M 函数可供数据工厂用户使用。

整理数据流将 Power Query Online 混合编辑器生成的 M 转换为用于云缩放执行的 spark 代码。

整理数据流对数据工程师或 "公民数据集成商" 特别有用。

## <a name="use-cases"></a>用例

### <a name="fast-interactive-data-exploration-and-preparation"></a>快速交互式数据探索和准备

多个数据工程师和公民数据集成商可以在云规模上以交互方式浏览和准备数据集。 由于数据 lake 中的数据量、种类和速度的增长，用户需要一种有效的方法来浏览和准备数据集。 例如，你可能需要创建一个数据集，该数据集包含自2017以来为新客户提供的所有客户人口统计信息。 你不会映射到已知目标。 在 lake 中发布之前，要浏览、整理和准备数据集，以满足要求。 整理数据流通常用于不太正式的分析方案。 准备好数据集可用于执行转换和机器学习操作下游。

### <a name="code-free-agile-data-preparation"></a>无代码敏捷数据准备

公民数据集成商花费超过60% 的时间来查找和准备数据。 他们希望以一种代码免费的方式来提高运营效率。 允许公民数据集成商使用已知的工具（如 Power Query 以可缩放的方式在线）来丰富、调整和发布数据，大大提高了工作效率。 Azure 数据工厂中的整理数据流支持熟悉的 Power Query Online 混合编辑器，使公民数据集成人员能够快速修复错误，使数据标准化，并生成高质量的数据来支持业务决策。

### <a name="data-validation"></a>数据验证

以无代码方式浏览数据，以删除任何离群值和异常，并将其与用于快速分析的形状相符。

## <a name="supported-sources"></a>支持的源

| 连接器 | 数据格式 | 身份验证类型 |
| -- | -- | --|
| [Azure Blob 存储](connector-azure-blob-storage.md) | CSV，Parquet | 帐户密钥 |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | CSV | Service Principal |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | CSV，Parquet | 帐户密钥、服务主体 |
| [Azure SQL 数据库](connector-azure-sql-database.md) | - | SQL 身份验证 |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) | - | SQL 身份验证 |

## <a name="the-mashup-editor"></a>混合编辑器

创建整理数据流时，所有源数据集都将成为数据集查询，并放在**ADFResource**文件夹中。 默认情况下，UserQuery 将指向第一个数据集查询。 所有转换都应在 UserQuery 上完成，因为不支持对数据集查询进行更改，也不会将其保留。 当前不支持重命名、添加和删除查询。

![整理](media/wrangling-data-flow/editor.png)

当前并非所有 Power Query M 函数都支持数据整理，但在创作期间可用。 生成整理数据流时，如果函数不受支持，系统将提示你输入以下错误消息：

`The wrangling data flow is invalid. Expression.Error: The transformation logic isn't supported. Please try a simpler expression`

有关支持的转换的详细信息，请参阅[整理数据流函数](wrangling-data-flow-functions.md)。

当前整理数据流仅支持写入到一个接收器。

## <a name="next-steps"></a>后续步骤

了解如何[创建整理](wrangling-data-flow-tutorial.md)数据流。