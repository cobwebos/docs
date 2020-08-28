---
title: 通过预测对机器学习模型进行评分
description: 了解如何使用 Synapse SQL 中的 T-sql PREDICT 函数对机器学习模型进行评分。
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 07/21/2020
ms.author: anjangsh
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: ef56274e0bda3f1a9d494852520a77ecdfc25799
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2020
ms.locfileid: "89048000"
---
# <a name="score-machine-learning-models-with-predict"></a>通过预测对机器学习模型进行评分

Synapse SQL 提供使用熟悉的 T-sql 语言对机器学习模型进行评分的功能。 利用 T-sql [预测](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest)，你可以将现有机器学习模型与历史数据定型，并将其评分到数据仓库的安全边界内。 PREDICT 函数使用 [ONNX (打开神经网络交换) ](https://onnx.ai/) 模型和数据作为输入。 此功能消除了在数据仓库外移动宝贵数据以实现评分的步骤。 它旨在使数据专业人员能够轻松地使用熟悉的 T-sql 界面来部署机器学习模型，并与数据科学家无缝协作，为其任务使用正确的框架。

> [!NOTE]
> 此功能当前在 SQL 点播情况下不受支持。

此功能要求在 Synapse SQL 之外训练该模型。 生成模型后，将其加载到数据仓库中，并通过 T-sql 预测语法对其进行评分，以获取数据的见解。

![predictoverview](./media/sql-data-warehouse-predict/datawarehouse-overview.png)

## <a name="training-the-model"></a>定型模型

Synapse SQL 需要预先训练的模型。 培训用于在 Synapse SQL 中执行预测的机器学习模型时，请记住以下因素。

- Synapse SQL 仅支持 ONNX 格式模型。 ONNX 是一种开源模型格式，可用于在不同框架之间交换模型以实现互操作性。 你可以使用框架将现有模型转换为 ONNX 格式，这种框架既支持本机支持，也可用来转换可用的包。 例如， [spark-sklearn-onnx](https://github.com/onnx/sklearn-onnx) package 将 scikit-learn-了解模型转换为 onnx。 [ONNX GitHub 存储库](https://github.com/onnx/tutorials#converting-to-onnx-format) 提供支持的框架和示例的列表。

   如果使用 [自动 ML](https://docs.microsoft.com/azure/machine-learning/concept-automated-ml) 进行培训，请确保将 *enable_onnx_compatible_models* 参数设置为 TRUE，以生成 onnx 格式模型。 [自动机器学习笔记本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) 显示了如何使用 AUTOML 创建 ONNX 格式的机器学习模型的示例。

- 输入数据支持以下数据类型：
    - int、bigint、real、float
    - char、varchar、nvarchar

- 评分数据需要与定型数据的格式相同。 预测不支持复杂数据类型（如多维数组）。 因此，对于定型，请确保模型的每个输入对应于评分表的单个列，而不是传递包含所有输入的单个数组。

- 请确保模型输入的名称和数据类型与新预测数据的列名称和数据类型匹配。 使用联机可用的各种开源工具可视化 ONNX 模型可以进一步帮助进行调试。

## <a name="loading-the-model"></a>正在加载模型

模型以十六进制字符串的形式存储在 Synapse SQL 用户表中。 可以在模型表中添加其他列（如 ID 和说明）来标识模型。 使用 varbinary (max) 作为模型列的数据类型。 下面是可用于存储模型的表的代码示例：

```sql
-- Sample table schema for storing a model and related data
CREATE TABLE [dbo].[Models]
(
    [Id] [int] IDENTITY(1,1) NOT NULL,
    [Model] [varbinary](max) NULL,
    [Description] [varchar](200) NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN,
    HEAP
)
GO

```

一旦将模型转换为十六进制字符串并指定了表定义，就可以使用 [COPY 命令](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) 或 Polybase 加载 Synapse SQL 表中的模型。 下面的代码示例使用 Copy 命令来加载模型。

```sql
-- Copy command to load hexadecimal string of the model from Azure Data Lake storage location
COPY INTO [Models] (Model)
FROM '<enter your storage location>'
WITH (
    FILE_TYPE = 'CSV',
    CREDENTIAL=(IDENTITY= 'Shared Access Signature', SECRET='<enter your storage key here>')
)
```

## <a name="scoring-the-model"></a>为模型评分

一旦将模型和数据加载到数据仓库中，就可以使用 **T-SQL 预测** 函数对模型进行评分。 请确保新的输入数据的格式与用于生成模型的定型数据的格式相同。 T-sql 预测采用两个输入：模型和新的评分输入数据，并为输出生成新列。可以将模型指定为变量、文本或标量 sub_query。 [与 Common_table_expression 一起](https://docs.microsoft.com/sql/t-sql/queries/with-common-table-expression-transact-sql?view=sql-server-ver15)使用，以便为数据参数指定命名的结果集。

下面的示例演示了一个使用预测函数的示例查询。 创建了包含预测结果的具有名称 *分数* 和数据类型 *float* 的其他列。 所有输入数据列以及输出预测列都可与 select 语句一起显示。 有关更多详细信息，请参阅 [预测 (transact-sql) ](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest)。

```sql
-- Query for ML predictions
SELECT d.*, p.Score
FROM PREDICT(MODEL = (SELECT Model FROM Models WHERE Id = 1),
DATA = dbo.mytable AS d) WITH (Score float) AS p;
```

## <a name="next-steps"></a>后续步骤

若要了解有关 PREDICT 函数的详细信息，请参阅 [预测 (transact-sql) ](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest)。
