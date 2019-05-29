---
title: 教程：准备数据以在 R 中训练预测模型
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: 在这个由三部分组成的教程系列的第一部分中，需要通过 Azure SQL 数据库准备数据，以便使用 Azure SQL 数据库机器学习服务（预览版）在 R 中训练预测模型。
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
ms.openlocfilehash: aa9c41ee34a50ab9b1409357bfe7d123166601bf
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2019
ms.locfileid: "65978732"
---
# <a name="tutorial-prepare-data-to-train-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>教程：准备数据以使用 Azure SQL 数据库机器学习服务（预览版）在 R 中训练预测模型

在这个由三部分组成的教程系列的第一部分中，需要通过 Azure SQL 数据库准备数据，以便使用 Azure SQL 数据库机器学习服务（预览版）在 R 中训练预测模型。

在本教程系列中，假设你拥有一家滑雪用具租赁公司，并且希望预测未来某一日期的租用数量。 此信息将帮助你做好库存、员工和设施方面的准备。

本文将介绍如何执行以下操作：

> [!div class="checklist"]
> * 将示例数据库导入 Azure SQL 数据库
> * 使用 R 将 Azure SQL 数据库中的数据加载到数据帧中
> * 通过将某些列标识为类别列来准备数据

[第二部分](sql-database-tutorial-predictive-model-build-compare.md)介绍如何创建和训练多个模型以及随后如何选择最准确的模型。

[第三部分](sql-database-tutorial-predictive-model-deploy.md)介绍如何将模型存储在数据库中，然后创建一个可根据新数据进行预测的存储过程。

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - 如果没有 Azure 订阅，请在开始前[创建帐户](https://azure.microsoft.com/free/)。

* 启用了机器学习服务的 Azure SQL 数据库服务器 - 在公共预览版期间，Microsoft 会将你加入预览并为你的现有数据库或新数据库启用机器学习。 执行[注册预览版](sql-database-machine-learning-services-overview.md#signup)中的步骤。

* RevoScaleR 包 - 有关在本地安装此包的选项，请参阅 [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler?view=sql-server-2017#versions-and-platforms)。

* R IDE - 此教程使用 [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/)。

* SQL 查询工具 - 此教程假设使用的是 [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) 或 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS)。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com/)。

## <a name="import-the-sample-database"></a>导入示例数据库

本教程中使用的示例数据集已保存到 .bacpac 数据库备份文件中，可供下载和使用  。

1. 请下载文件 [TutorialDB.bacpac](https://sqlchoice.blob.core.windows.net/sqlchoice/static/TutorialDB.bacpac)。

1. 按照[导入 BACPAC 文件以创建 Azure SQL 数据库](https://docs.microsoft.com/azure/sql-database/sql-database-import)中的说明执行操作，并使用以下详细信息：

   * 从下载的 TutorialDB.bacpac 文件进行导入 
   * 在公共预览版中，为新数据库选择 Gen5/vCore 配置 
   * 将新数据库命名为“TutorialDB”

## <a name="load-the-data-into-a-data-frame"></a>将数据加载到数据帧

要在 R 中使用这些数据，请将 Azure SQL 数据库中的数据加载到数据帧 (`rentaldata`) 中。

在 RStudio 中创建新的 RScript 文件并运行以下脚本。 使用自己的连接信息替换“服务器”、“UID”和“PWD”    。

```r
#Define the connection string to connect to the TutorialDB database
connStr <- paste("Driver=SQL Server",
               "; Server=", "<Azure SQL Database Server>",
               "; Database=TutorialDB",
               "; UID=", "<user>",
               "; PWD=", "<password>",
                  sep = "");

#Get the data from the table
SQL_rentaldata <- RxSqlServerData(table = "dbo.rental_data", connectionString = connStr, returnDataFrame = TRUE);

#Import the data into a data frame
rentaldata <- rxImport(SQL_rentaldata);

#Take a look at the structure of the data and the top rows
head(rentaldata);
str(rentaldata);
```

应看到如下所示的结果。

```results
   Year  Month  Day  RentalCount  WeekDay  Holiday  Snow
1  2014    1     20      445         2        1      0
2  2014    2     13       40         5        0      0
3  2013    3     10      456         1        0      0
4  2014    3     31       38         2        0      0
5  2014    4     24       23         5        0      0
6  2015    2     11       42         4        0      0
'data.frame':       453 obs. of  7 variables:
$ Year       : int  2014 2014 2013 2014 2014 2015 2013 2014 2013 2015 ...
$ Month      : num  1 2 3 3 4 2 4 3 4 3 ...
$ Day        : num  20 13 10 31 24 11 28 8 5 29 ...
$ RentalCount: num  445 40 456 38 23 42 310 240 22 360 ...
$ WeekDay    : num  2 5 1 2 5 4 1 7 6 1 ...
$ Holiday    : int  1 0 0 0 0 0 0 0 0 0 ...
$ Snow       : num  0 0 0 0 0 0 0 0 0 0 ...
```

## <a name="prepare-the-data"></a>准备数据

在此示例数据库中，大部分准备工作已经完成，但你需在此处再做一次准备工作。
通过将数据类型更改为“因素”，使用下面的 R 脚本将三列标识为“类别”   。

```r
#Changing the three factor columns to factor types
rentaldata$Holiday <- factor(rentaldata$Holiday);
rentaldata$Snow    <- factor(rentaldata$Snow);
rentaldata$WeekDay <- factor(rentaldata$WeekDay);

#Visualize the dataset after the change
str(rentaldata);
```

应看到如下所示的结果。

```results
data.frame':      453 obs. of  7 variables:
$ Year       : int  2014 2014 2013 2014 2014 2015 2013 2014 2013 2015 ...
$ Month      : num  1 2 3 3 4 2 4 3 4 3 ...
$ Day        : num  20 13 10 31 24 11 28 8 5 29 ...
$ RentalCount: num  445 40 456 38 23 42 310 240 22 360 ...
$ WeekDay    : Factor w/ 7 levels "1","2","3","4",..: 2 5 1 2 5 4 1 7 6 1 ...
$ Holiday    : Factor w/ 2 levels "0","1": 2 1 1 1 1 1 1 1 1 1 ...
$ Snow       : Factor w/ 2 levels "0","1": 1 1 1 1 1 1 1 1 1 1 ...
```

数据现在已准备好供培训使用。

## <a name="clean-up-resources"></a>清理资源

如果不打算继续学习本教程，请从 Azure SQL 数据库服务器中删除 TutorialDB 数据库。

在 Azure 门户中执行以下步骤：

1. 从 Azure 门户的左侧菜单中，选择“所有资源”或“SQL 数据库”   。
1. 在“按名称筛选...”字段中，输入“TutorialDB”，然后选择你的订阅   。
1. 选择 TutorialDB 数据库。
1. 在“概览”  页上，选择“删除”  。

## <a name="next-steps"></a>后续步骤

在本教程系列的第一部分，你已完成以下步骤：

* 将数据库备份文件导入 Azure SQL 数据库
* 使用 R 将 Azure SQL 数据库中的数据加载到数据帧中
* 通过将某些列标识为类别来准备数据

若要创建使用 TutorialDB 数据库中数据的机器学习模型，请按照本教程系列的第二部分执行操作：

> [!div class="nextstepaction"]
> [教程：使用 Azure SQL 数据库机器学习服务（预览版）在 R 中创建预测模型](sql-database-tutorial-predictive-model-build-compare.md)
