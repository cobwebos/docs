---
title: 教程：在 R 中部署预测模型
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: 在这个由三部分组成的教程的第三部分中，使用 Azure SQL 数据库机器学习服务（预览版）在 R 中部署预测模型。
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: tutorial
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 05/02/2019
ms.openlocfilehash: 17b68f71f4034e5eb637d40b975cc22d94438fb7
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2019
ms.locfileid: "65978709"
---
# <a name="tutorial-deploy-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>教程：使用 Azure SQL 数据库机器学习服务（预览版）在 R 中部署预测模型

在这个由三部分组成的教程的第三部分中，使用 Azure SQL 数据库机器学习服务（预览版）在 R 中部署预测模型。

将使用嵌入式 R 脚本创建存储过程，该脚本使用模型进行预测。 由于模型在 Azure SQL 数据库中执行，因此可用存储在数据库中的数据轻松地对其进行训练。

本文将介绍如何执行以下操作：

> [!div class="checklist"]
> * 将预测模型存储在数据库表中
> * 创建生成模型的存储过程
> * 创建使用模型进行预测的存储过程
> * 使用新数据执行模型

[第一部分](sql-database-tutorial-predictive-model-prepare-data.md)介绍如何将示例数据库导入 Azure SQL 数据库，然后准备用于在 R 中训练预测模型的数据。

[第二部分](sql-database-tutorial-predictive-model-build-compare.md)介绍如何创建和训练多个模型，然后选择最准确的模型。

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>先决条件

* 本系列教程的第三部分假定你已完成[第一部分](sql-database-tutorial-predictive-model-prepare-data.md)和[第二部分](sql-database-tutorial-predictive-model-build-compare.md)   。

## <a name="create-a-stored-procedure-that-generates-the-model"></a>创建生成模型的存储过程

在本教程系列的第二部分中，认为决策树 (dtree) 模型是最准确的。 现在创建一个存储过程 (`generate_rental_rx_model`)，它使用 RevoScaleR 包中的 rxDTree 训练并生成 dtree 模型。

在 Azure Data Studio 或 SSMS 中运行以下命令。

```sql
-- Stored procedure that trains and generates an R model using the rental_data and a decision tree algorithm
DROP PROCEDURE IF EXISTS generate_rental_rx_model;
GO
CREATE PROCEDURE generate_rental_rx_model (@trained_model VARBINARY(max) OUTPUT)
AS
BEGIN
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
require("RevoScaleR");

rental_train_data$Holiday <- factor(rental_train_data$Holiday);
rental_train_data$Snow    <- factor(rental_train_data$Snow);
rental_train_data$WeekDay <- factor(rental_train_data$WeekDay);

#Create a dtree model and train it using the training data set
model_dtree <- rxDTree(RentalCount ~ Month + Day + WeekDay + Snow + Holiday, data = rental_train_data);
#Serialize the model before saving it to the database table
trained_model <- as.raw(serialize(model_dtree, connection=NULL));
'
        , @input_data_1 = N'
            SELECT RentalCount
                 , Year
                 , Month
                 , Day
                 , WeekDay
                 , Snow
                 , Holiday
            FROM dbo.rental_data
            WHERE Year < 2015
            '
        , @input_data_1_name = N'rental_train_data'
        , @params = N'@trained_model varbinary(max) OUTPUT'
        , @trained_model = @trained_model OUTPUT;
END;
GO
```

## <a name="store-the-model-in-a-database-table"></a>将模型存储在数据库表中

在 TutorialDB 数据库中创建一个表，然后将模型保存到表中。

1. 创建表 (`rental_rx_models`) 来存储模型。

    ```sql
    USE TutorialDB;
    DROP TABLE IF EXISTS rental_rx_models;
    GO
    CREATE TABLE rental_rx_models (
          model_name VARCHAR(30) NOT NULL DEFAULT('default model') PRIMARY KEY
        , model VARBINARY(MAX) NOT NULL
        );
    GO
    ```

1. 将模型作为二进制对象保存到表中，模型命名为“rxDTree”。

    ```sql
    -- Save model to table
    TRUNCATE TABLE rental_rx_models;
    
    DECLARE @model VARBINARY(MAX);
    
    EXECUTE generate_rental_rx_model @model OUTPUT;
    
    INSERT INTO rental_rx_models (
          model_name
        , model
        )
    VALUES (
         'rxDTree'
        , @model
        );
    
    SELECT *
    FROM rental_rx_models;
    ```

## <a name="create-a-stored-procedure-that-makes-predictions"></a>创建进行预测的存储过程

创建存储过程 (`predict_rentalcount_new`)，使用训练的模型和一组新数据进行预测。

```sql
-- Stored procedure that takes model name and new data as input parameters and predicts the rental count for the new data
DROP PROCEDURE IF EXISTS predict_rentalcount_new;
GO
CREATE PROCEDURE predict_rentalcount_new (
      @model_name VARCHAR(100)
    , @input_query NVARCHAR(MAX)
    )
AS
BEGIN
    DECLARE @rx_model VARBINARY(MAX) = (
            SELECT model
            FROM rental_rx_models
            WHERE model_name = @model_name
            );

    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
require("RevoScaleR");

#Convert types to factors
rentals$Holiday <- factor(rentals$Holiday);
rentals$Snow    <- factor(rentals$Snow);
rentals$WeekDay <- factor(rentals$WeekDay);

#Before using the model to predict, we need to unserialize it
rental_model <- unserialize(rx_model);

#Call prediction function
rental_predictions <- rxPredict(rental_model, rentals);
'
        , @input_data_1 = @input_query
        , @input_data_1_name = N'rentals'
        , @output_data_1_name = N'rental_predictions'
        , @params = N'@rx_model varbinary(max)'
        , @rx_model = @rx_model
    WITH RESULT SETS(("RentalCount_Predicted" FLOAT));
END;
GO
```

## <a name="execute-the-model-with-new-data"></a>使用新数据执行模型

现在，可使用存储过程 `predict_rentalcount_new` 来预测新数据的租借计数。

```sql
-- Use the predict_rentalcount_new stored procedure with the model name and a set of features to predict the rental count
EXECUTE dbo.predict_rentalcount_new @model_name = 'rxDTree'
    , @input_query = '
        SELECT CONVERT(INT,  3) AS Month
             , CONVERT(INT, 24) AS Day
             , CONVERT(INT,  4) AS WeekDay
             , CONVERT(INT,  1) AS Snow
             , CONVERT(INT,  1) AS Holiday
        ';
GO
```

应看到类似于以下的结果。

```results
RentalCount_Predicted
332.571428571429
```

已在 Azure SQL 数据库中成功创建、培训和部署模型。 然后在存储过程中使用该模型来基于新数据预测值。

## <a name="clean-up-resources"></a>清理资源

使用完 TutorialDB 数据库后，将其从 Azure SQL 数据库服务器中删除。

在 Azure 门户中执行以下步骤：

1. 从 Azure 门户的左侧菜单中，选择“所有资源”或“SQL 数据库”   。
1. 在“按名称筛选...”字段中，输入“TutorialDB”，然后选择你的订阅   。
1. 选择 TutorialDB 数据库。
1. 在“概览”  页上，选择“删除”  。

## <a name="next-steps"></a>后续步骤

在本教程系列的第三部分，你已完成以下步骤：

* 将预测模型存储在数据库表中
* 创建生成模型的存储过程
* 创建使用模型进行预测的存储过程
* 使用新数据执行模型

要了解有关在 Azure SQL 数据库机器学习服务（预览版）中使用 R 的详细信息，请参阅：

* [使用机器学习服务（预览版）在 Azure SQL 数据库中编写高级 R 函数](sql-database-machine-learning-services-functions.md)
* [在 Azure SQL 数据库机器学习服务中使用 R 和 SQL 数据（预览版）](sql-database-machine-learning-services-data-issues.md)
* [将 R 包添加到 Azure SQL 数据库机器学习服务（预览版）](sql-database-machine-learning-services-add-r-packages.md)
