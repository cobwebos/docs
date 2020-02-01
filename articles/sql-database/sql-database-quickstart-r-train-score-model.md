---
title: 使用 R 创建并训练预测模型
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: 通过 Azure SQL 数据库机器学习服务（预览版）使用 R 创建一个简单的预测模型，然后使用新数据预测结果。
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/11/2019
ms.openlocfilehash: a54d418f668d8c7292c8332c1b14c4df45e59308
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2020
ms.locfileid: "76768466"
---
# <a name="quickstart-create-and-train-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>快速入门：通过 Azure SQL 数据库机器学习服务（预览版）使用 R 创建并训练预测模型

在本快速入门中，我们使用 R 创建并训练一个预测模型，将该模型保存到数据库的表中，然后通过 Azure SQL 数据库中的机器学习服务（使用 R）使用该模型基于新数据来预测值。

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>必备条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- 一个使用[服务器级防火墙规则](sql-database-server-level-firewall-rule.md)的 [Azure SQL 数据库](sql-database-single-database-get-started.md)
- 启用了 R 的[机器学习服务](sql-database-machine-learning-services-overview.md)。 [注册预览版](sql-database-machine-learning-services-overview.md#signup)。
- [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS)

> [!NOTE]
> 在发布公共预览版期间，Microsoft 会将你加入该版本并为你的现有数据库或新数据库启用机器学习。

此示例使用 R 随附的 **cars** 数据集，通过一个简单的回归模型根据速度预测汽车的制动距离。

> [!TIP]
> R 运行时随附了许多数据集。若要获取已安装数据集的列表，请在 R 命令提示符下键入 `library(help="datasets")`。

## <a name="create-and-train-a-predictive-model"></a>创建并训练预测模型

**cars** 数据集中的汽车速度数据包含两个数字列：**dist** 和 **speed**。 此数据包括不同速度下的多个制动观察值。 你将基于此数据创建一个线性回归模型，用于描述汽车速度与汽车制动所需距离之间的关系。

线性模型的要求很简单：
- 定义一个公式，用于描述因变量 *speed* 与自变量 *distance* 之间的关系。
- 提供用于训练模型的输入数据。

> [!TIP]
> 如果需要对线性模型进行回顾，请尝试以下教程，其中介绍了使用 rxLinMod 拟合模型的过程：[拟合线性模型](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-linear-model)

在下面的步骤中，你将设置训练数据，创建一个回归模型，使用训练数据对其进行训练，然后将该模型保存到一个 SQL 表。

1. 打开 **SQL Server Management Studio**，连接到 SQL 数据库。

   如果在连接方面需要帮助，请参阅[快速入门：使用 SQL Server Management Studio 连接和查询 Azure SQL 数据库](sql-database-connect-query-ssms.md)。

1. 创建 **CarSpeed** 表来保存训练数据。

    ```sql
    CREATE TABLE dbo.CarSpeed (
        speed INT NOT NULL
        , distance INT NOT NULL
        )
    GO
    
    INSERT INTO dbo.CarSpeed (
        speed
        , distance
        )
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'car_speed <- cars;'
        , @input_data_1 = N''
        , @output_data_1_name = N'car_speed'
    GO
    ```

1. 使用 `rxLinMod` 创建一个回归模型。 

   为了构建该模型，你需要在 R 代码内定义公式，然后将训练数据 **CarSpeed** 作为输入参数进行传递。

    ```sql
    DROP PROCEDURE IF EXISTS generate_linear_model;
    GO
    CREATE PROCEDURE generate_linear_model
    AS
    BEGIN
      EXECUTE sp_execute_external_script
      @language = N'R'
      , @script = N'
    lrmodel <- rxLinMod(formula = distance ~ speed, data = CarsData);
    trained_model <- data.frame(payload = as.raw(serialize(lrmodel, connection=NULL)));
    '
      , @input_data_1 = N'SELECT [speed], [distance] FROM CarSpeed'
      , @input_data_1_name = N'CarsData'
      , @output_data_1_name = N'trained_model'
      WITH RESULT SETS ((model VARBINARY(max)));
    END;
    GO
    ```

     rxLinMod 的第一个参数是 *formula* 参数，它将距离定义为速度的因变量。 输入数据存储在由 SQL 查询填充的 `CarsData` 变量中。

1. 创建一个用来存储模型的表，以便稍后可以使用模型进行预测。 

   创建模型的 R 程序包的输出通常是一个**二进制对象**，因此该表必须有一个 **VARBINARY(max)** 类型的列。

    ```sql
    CREATE TABLE dbo.stopping_distance_models (
        model_name VARCHAR(30) NOT NULL DEFAULT('default model') PRIMARY KEY
        , model VARBINARY(max) NOT NULL
        );
    ```

1. 现在，调用存储过程，生成模型，将其保存到一个表中。

   ```sql
   INSERT INTO dbo.stopping_distance_models (model)
   EXECUTE generate_linear_model;
   ```

   请注意，如果再次运行该代码，则会出现以下错误：

   ```text
   Violation of PRIMARY KEY constraint...Cannot insert duplicate key in object bo.stopping_distance_models
   ```

   若要避免此错误，一种选择是更新每个新模型的名称。 例如，可以将名称更改为更具说明性的名称，并且包括模型类型、创建日期等。

   ```sql
   UPDATE dbo.stopping_distance_models
   SET model_name = 'rxLinMod ' + FORMAT(GETDATE(), 'yyyy.MM.HH.mm', 'en-gb')
   WHERE model_name = 'default model'
   ```

## <a name="view-the-table-of-coefficients"></a>查看系数表

通常情况下，存储过程 [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) 的 R 输出仅限单个数据帧。 但是，可以返回除数据帧之外的其他类型的输出，例如标量。

例如，假设你需要训练一个模型，但需要立即查看模型的系数表。 为此，请创建系数表作为主结果集，然后在 SQL 变量中输出已训练的模型。 可以通过调用该变量来立即重用模型，也可以将模型保存到表中，如下所示。

```sql
DECLARE @model VARBINARY(max)
    , @modelname VARCHAR(30)

EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
speedmodel <- rxLinMod(distance ~ speed, CarsData)
modelbin <- serialize(speedmodel, NULL)
OutputDataSet <- data.frame(coefficients(speedmodel));
'
    , @input_data_1 = N'SELECT [speed], [distance] FROM CarSpeed'
    , @input_data_1_name = N'CarsData'
    , @params = N'@modelbin varbinary(max) OUTPUT'
    , @modelbin = @model OUTPUT
WITH RESULT SETS(([Coefficient] FLOAT NOT NULL));

-- Save the generated model
INSERT INTO dbo.stopping_distance_models (
    model_name
    , model
    )
VALUES (
    'latest model'
    , @model
    )
```

**结果**

![具有额外输出的已训练模型](./media/sql-database-quickstart-r-train-score-model/r-train-model-with-additional-output.png)

## <a name="score-new-data-using-the-trained-model"></a>使用已定型的模型对新数据进行评分

评分是数据科学领域使用的术语，用于表示根据馈送到已定型的模型中的新数据生成预测、概率或其他值  。 你将使用在上一节中创建的模型来对新数据的预测进行评分。

你是否注意到，原始训练数据的最高速度是 25 英里/小时？ 这是因为原始数据基于 1920 年的一次试验！ 你可能想知道，如果一辆上世纪 20 年代的汽车可以达到 60 mph 甚至 100 mph 的速度，其制动时间会是多长？ 要回答此问题，你可以向模型提供一些新的速度值。

1. 创建一个表来包含新的速度数据。

   ```sql
    CREATE TABLE dbo.NewCarSpeed (
        speed INT NOT NULL
        , distance INT NULL
        )
    GO
    
    INSERT dbo.NewCarSpeed (speed)
    VALUES (40)
        , (50)
        , (60)
        , (70)
        , (80)
        , (90)
        , (100)
   ```

2. 基于这些新的速度值预测制动距离。

   由于模型基于 **RevoScaleR** 包中提供的 **rxLinMod** 算法，因此调用 [rxPredict](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxpredict) 函数而不是 R 的泛型 `predict` 函数。

   此示例脚本：
   - 使用 SELECT 语句从表中获取单个模型
   - 将其作为输入参数传递
   - 在模型上调用 `unserialize` 函数
   - 将 `rxPredict` 函数及合适的参数应用于模型
   - 提供新的输入数据

   > [!TIP]
   > 若要进行实时评分，请参阅 RevoScaleR 提供的[序列化函数](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxserializemodel)。

   ```sql
    DECLARE @speedmodel VARBINARY(max) = (
            SELECT model
            FROM dbo.stopping_distance_models
            WHERE model_name = 'latest model'
            );
    
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    current_model <- unserialize(as.raw(speedmodel));
    new <- data.frame(NewCarData);
    predicted.distance <- rxPredict(current_model, new);
    str(predicted.distance);
    OutputDataSet <- cbind(new, ceiling(predicted.distance));
    '
        , @input_data_1 = N'SELECT speed FROM [dbo].[NewCarSpeed]'
        , @input_data_1_name = N'NewCarData'
        , @params = N'@speedmodel varbinary(max)'
        , @speedmodel = @speedmodel
    WITH RESULT SETS((
                new_speed INT
                , predicted_distance INT
                ));
   ```

   **结果**

   ![预测制动距离的结果集](./media/sql-database-quickstart-r-train-score-model/r-predict-stopping-distance-resultset.png)

> [!NOTE]
> 在此示例脚本中，`str` 函数是在测试阶段添加的，用于检查从 R 返回的数据的架构。稍后可以删除该语句。
>
> R 脚本中使用的列名不必传递到存储过程输出。 此处，WITH RESULTS 子句定义了一些新的列名。

## <a name="next-steps"></a>后续步骤

有关使用 R 的 Azure SQL 数据库机器学习服务（预览版）的详细信息，请参阅以下文章。

- [使用 R 的 Azure SQL 数据库机器学习服务（预览版）](sql-database-machine-learning-services-overview.md)
- [在 Azure SQL 数据库机器学习服务（预览版）中创建和运行简单的 R 脚本](sql-database-quickstart-r-create-script.md)
- [使用机器学习服务（预览版）在 Azure SQL 数据库中编写高级 R 函数](sql-database-machine-learning-services-functions.md)
- [在 Azure SQL 数据库机器学习服务中使用 R 和 SQL 数据（预览版）](sql-database-machine-learning-services-data-issues.md)
