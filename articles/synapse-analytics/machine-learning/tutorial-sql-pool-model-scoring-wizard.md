---
title: 教程：用于 SQL 池的机器学习模型评分向导
description: 有关如何使用机器学习模型评分向导来扩充 Synapse SQL 池中的数据的教程
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 09/25/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 14358f60b700e80c17b1dd8259f1d0566a854c43
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91542545"
---
# <a name="tutorial-machine-learning-model-scoring-wizard-for-synapse-sql-pools"></a>教程：用于 Synapse SQL 池的机器学习模型评分向导

了解如何借助预测性机器学习模型轻松扩充 SQL 池中的数据。  数据专业人员现在可以轻松使用数据科学家创建的模型进行预测分析。 Synapse 中的数据专业人员可以直接从 Azure 机器学习模型注册表中选择一个模型，以便在 Synapse SQL 池中进行部署，并启动预测来扩充数据。

在本教程中，你将了解：

> [!div class="checklist"]
> - 训练一个预测性机器学习模型并在 Azure 机器学习模型注册表中注册该模型
> - 使用 SQL 评分向导在 Synapse SQL 池中启动预测

如果没有 Azure 订阅，请[在开始之前创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

- [Synapse Analytics 工作区](../get-started-create-workspace.md)，其中 ADLS Gen2 存储帐户配置为默认存储。 你必须是所用 ADLS Gen2 文件系统的存储 Blob 数据参与者。
- Synapse Analytics 工作区中的 Synapse SQL 池。 有关详细信息，请参阅[创建 Synapse SQL 池](../quickstart-create-sql-pool-studio.md)。
- Synapse Analytics 工作区中的 Azure 机器学习链接服务。 有关详细信息，请参阅[在 Synapse 中创建 Azure 机器学习链接服务](quickstart-integrate-azure-machine-learning.md)。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com/)

## <a name="train-a-model-in-azure-machine-learning"></a>在 Azure 机器学习中训练模型

在开始之前，请验证你的 **sklearn** 版本是否为 0.20.3。

运行笔记本中的所有单元之前，请检查计算实例是否正在运行。

![验证 AML 计算](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00b.png)

1. 导航到你的 Azure 机器学习工作区。

1. 下载 [Predict NYC Taxi Tips.ipynb](https://go.microsoft.com/fwlink/?linkid=2144301)。

1. 在 [Azure 机器学习工作室](https://ml.azure.com)中启动 Azure 机器学习工作区。

1. 转到“笔记本”并单击“上传文件”，选择你下载的“Predict NYC Taxi Tips.ipynb”并上传该文件。
   ![上传文件](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00a.png)

1. 在笔记本上传并打开后，单击“运行所有单元”。

   其中一个单元可能会失败并要求你向 Azure 进行身份验证。 请在单元输出中注意此信息，在浏览器中使用以下链接并输入代码进行身份验证。 然后，再次运行笔记本。

1. 该笔记本会训练 ONNX 模型并将其注册到 MLFlow。 转到“模型”来检查新模型是否已正确注册。
   ![注册表中的模型](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00c.png)

1. 运行笔记本还会将测试数据导出到 CSV 文件中。 将 CSV 文件下载到本地系统。 稍后，你将 CSV 文件导入到 SQL 池中并使用该数据来测试模型。

   CSV 文件是在笔记本文件所在的文件夹中创建的。 如果没有立刻看到该文件，请在文件资源管理器中单击“刷新”。

   ![CSV 文件](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00d.png)

## <a name="launch-predictions-with-sql-scoring-wizard"></a>使用 SQL 评分向导启动预测

1. 使用 Synapse Studio 打开 Synapse 工作区。

1. 导肮到“数据” -> “链接” -> “存储帐户”。   将 `test_data.csv` 上传到默认存储帐户。

   ![上传数据](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00a.png)

1. 转到“开发” -> “SQL 脚本”。 创建一个新的 SQL 脚本以将 `test_data.csv` 上传到你的 SQL 池。

   > [!NOTE]
   > 在运行此脚本之前，请更新其中的文件 URL。

   ```SQL
   IF NOT EXISTS (SELECT * FROM sys.objects WHERE NAME = 'nyc_taxi' AND TYPE = 'U')
   CREATE TABLE dbo.nyc_taxi
   (
       tipped int,
       fareAmount float,
       paymentType int,
       passengerCount int,
       tripDistance float,
       tripTimeSecs bigint,
       pickupTimeBin nvarchar(30)
   )
   WITH
   (
       DISTRIBUTION = ROUND_ROBIN,
       CLUSTERED COLUMNSTORE INDEX
   )
   GO
   
   COPY INTO dbo.nyc_taxi
   (tipped 1, fareAmount 2, paymentType 3, passengerCount 4, tripDistance 5, tripTimeSecs 6, pickupTimeBin 7)
   FROM '<URL to linked storage account>/test_data.csv'
   WITH
   (
       FILE_TYPE = 'CSV',
       ROWTERMINATOR='0x0A',
       FIELDQUOTE = '"',
       FIELDTERMINATOR = ',',
       FIRSTROW = 2
   )
   GO
   
   SELECT TOP 100 * FROM nyc_taxi
   GO
   ```

   ![将数据加载到 SQL 池](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00b.png)

1. 转到“数据” -> “工作区”。 通过右键单击 SQL 池表打开 SQL 评分向导。 选择“机器学习” -> “使用现有模型进行扩充”。 

   > [!NOTE]
   > 除非为 Azure 机器学习创建了链接服务，否则不会出现机器学习选项（请参阅本教程开头部分的**先决条件**）。

   ![机器学习选项](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00c.png)

1. 在下拉框中，选择一个链接的 Azure 机器学习工作区。 这会从所选 Azure 机器学习工作区的模型注册表中加载机器学习模型的列表。 当前仅支持 ONNX 模型，因此这只会显示 ONNX 模型。

1. 选择你刚才训练的模型，然后单击“继续”。

   ![选择 Azure 机器学习模型](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00d.png)

1. 接下来，将表列映射到模型输入并指定模型输出。 如果模型是以 MLFlow 格式保存的并且模型签名已进行填充，则系统会使用基于名称相似性的逻辑为你自动执行映射。 此界面还支持手动映射。

   单击 **“继续”** 。

   ![表到模型的映射](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00e.png)

1. 生成的 T-SQL 代码包装在存储过程中。 这是你需要为存储过程提供名称的原因。 包含元数据（版本、说明等）的模型二进制文件会以物理方式从 Azure 机器学习复制到 SQL 池表。 因此，你需要指定要在其中保存模型的表。 你可以选择“使用现有表”，也可以选择“创建新表”。 在完成后，单击“部署模型 + 打开编辑器”以部署模型并生成 T-SQL 预测脚本。

   ![创建过程](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00f.png)

1. 生成脚本后，单击“运行”以执行评分操作并获取预测。

   ![运行预测](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00g.png)

## <a name="next-steps"></a>后续步骤

- [快速入门：在 Synapse 中创建新的 Azure 机器学习链接服务](quickstart-integrate-azure-machine-learning.md)
- [Azure Synapse Analytics 中的机器学习功能（工作区预览版）](what-is-machine-learning.md)
