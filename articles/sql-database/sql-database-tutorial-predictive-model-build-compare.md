---
title: 教程：在 R 中训练和比较预测模型
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: 在这个由三部分组成的教程系列的第二部分，你将使用 Azure SQL 数据库机器学习服务（预览版）在 R 中创建两个预测模型，然后选择最准确的模型。
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
ms.openlocfilehash: 3d336d6a53b6d234048c56d8492d278bef6fed64
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2019
ms.locfileid: "65957508"
---
# <a name="tutorial-create-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>教程：使用 Azure SQL 数据库机器学习服务（预览版）在 R 中创建预测模型

在这个由三部分组成的教程系列的第二部分，你将使用 Azure SQL 数据库机器学习服务（预览版）在 R 中创建两个预测模型，然后选择最准确的模型。

本文将介绍如何执行以下操作：

> [!div class="checklist"]
> * 训练两个机器学习模型
> * 通过这两个模型进行预测
> * 比较结果以选择最准确的模型

[第一部分](sql-database-tutorial-predictive-model-prepare-data.md)介绍如何将示例数据库导入 Azure SQL 数据库，然后准备用于在 R 中训练预测模型的数据。

[第三部分](sql-database-tutorial-predictive-model-deploy.md)介绍如何将模型存储在数据库中，然后创建一个可根据新数据进行预测的存储过程。

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>先决条件

* 本教程的第二部分假定你已完成[**第一部分**](sql-database-tutorial-predictive-model-prepare-data.md)及其先决条件。

## <a name="train-two-models"></a>训练两个模型

若要找出滑雪器具租赁数据的最佳模型，请创建两个不同的模型（线性回归和决策树），看哪个模型做出的预测更准确。 你将使用在本系列的第一部分创建的数据帧 `rentaldata`。

```r
#First, split the dataset into two different sets:
# one for training the model and the other for validating it
train_data = rentaldata[rentaldata$Year < 2015,];
test_data  = rentaldata[rentaldata$Year == 2015,];

#Use the RentalCount column to check the quality of the prediction against actual values
actual_counts <- test_data$RentalCount;

#Model 1: Use rxLinMod to create a linear regression model, trained with the training data set
model_linmod <- rxLinMod(RentalCount ~  Month + Day + WeekDay + Snow + Holiday, data = train_data);

#Model 2: Use rxDTree to create a decision tree model, trained with the training data set
model_dtree  <- rxDTree(RentalCount ~ Month + Day + WeekDay + Snow + Holiday, data = train_data);
```

## <a name="make-predictions-from-both-models"></a>通过这两个模型进行预测

使用预测函数通过每个已训练的模型预测租赁计数。

```r
#Use both models to make predictions using the test data set.
predict_linmod <- rxPredict(model_linmod, test_data, writeModelVars = TRUE, extraVarsToWrite = c("Year"));

predict_dtree  <- rxPredict(model_dtree,  test_data, writeModelVars = TRUE, extraVarsToWrite = c("Year"));

#To verify it worked, look at the top rows of the two prediction data sets.
head(predict_linmod);
head(predict_dtree);
```

```results
    RentalCount_Pred  RentalCount  Month  Day  WeekDay  Snow  Holiday
1         27.45858          42       2     11     4      0       0
2        387.29344         360       3     29     1      0       0
3         16.37349          20       4     22     4      0       0
4         31.07058          42       3      6     6      0       0
5        463.97263         405       2     28     7      1       0
6        102.21695          38       1     12     2      1       0
    RentalCount_Pred  RentalCount  Month  Day  WeekDay  Snow  Holiday
1          40.0000          42       2     11     4      0       0
2         332.5714         360       3     29     1      0       0
3          27.7500          20       4     22     4      0       0
4          34.2500          42       3      6     6      0       0
5         645.7059         405       2     28     7      1       0
6          40.0000          38       1     12     2      1       0
```

## <a name="compare-the-results"></a>比较结果

现在，你想了解哪个模型提供最佳预测。 一种快速简便的方法是使用基本绘图函数来查看训练数据中实际值与预测值之间的差异。

```r
#Use the plotting functionality in R to visualize the results from the predictions
par(mfrow = c(2, 1));
plot(predict_linmod$RentalCount_Pred - predict_linmod$RentalCount, main = "Difference between actual and predicted. rxLinmod");
plot(predict_dtree$RentalCount_Pred  - predict_dtree$RentalCount,  main = "Difference between actual and predicted. rxDTree");
```

![比较两个模型](./media/sql-database-tutorial-predictive-model-build-compare/compare-models.png)

看起来决策树模型是两个模型中更准确的。

## <a name="clean-up-resources"></a>清理资源

如果不打算继续学习本教程，请从 Azure SQL 数据库服务器中删除 TutorialDB 数据库。

在 Azure 门户中执行以下步骤：

1. 从 Azure 门户的左侧菜单中，选择“所有资源”或“SQL 数据库”   。
1. 在“按名称筛选...”字段中，输入“TutorialDB”，然后选择你的订阅   。
1. 选择 TutorialDB 数据库。
1. 在“概览”  页上，选择“删除”  。

## <a name="next-steps"></a>后续步骤

在本教程系列的第二部分，你已完成以下步骤：

* 训练两个机器学习模型
* 通过这两个模型进行预测
* 比较结果以选择最准确的模型

若要部署已创建的机器学习模型，请遵循本教程系列的第三部分：

> [!div class="nextstepaction"]
> [教程：使用 Azure SQL 数据库机器学习服务（预览版）在 R 中部署预测模型](sql-database-tutorial-predictive-model-deploy.md)
