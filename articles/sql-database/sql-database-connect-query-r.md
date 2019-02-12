---
title: 在 Azure SQL 数据库中使用机器学习服务（预览版，使用 R）的快速入门 | Microsoft Docs
description: 本主题介绍如何在 Azure SQL 数据库中使用机器学习服务以及如何运行 R 脚本来完成大规模高级分析，并介绍在不需跨网络拉取数据的情况下对数据进行就地计算和处理的功能。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: r
ms.topic: quickstart
author: dphansen
ms.author: davidph
ms.reviewer: ''
manager: cgronlun
ms.date: 01/31/2019
ms.openlocfilehash: 84017e95d41f8934de248065a2b66792628b41d2
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2019
ms.locfileid: "55815534"
---
# <a name="quickstart-use-machine-learning-services-with-r-in-azure-sql-database-preview"></a>快速入门：在 Azure SQL 数据库中使用机器学习服务（预览版，使用 R）

本文介绍如何[在 Azure SQL 数据库中使用机器学习服务（使用 R）](sql-database-machine-learning-services-overview.md)的公共预览版， 并介绍在 SQL 数据库和 R 之间移动数据的基本知识，同时还介绍如何将正确格式的 R 代码包装在存储过程 [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) 中，以便在 SQL 数据库中生成、训练和使用机器学习模型。

使用 R 语言的强大功能在数据库中提供高级分析和机器学习。 这种能力将计算和处理功能带到了数据所在的位置，不需要通过网络拉取数据。 另外，可以利用企业 R 包的强大功能大规模地提供高级分析。

机器学习服务包括了 R 的基本发行版，其中包含 Microsoft 提供的企业 R 包。 Microsoft 的 R 函数和算法是为规模和实用性而设计的，提供预测分析、统计建模、数据可视化以及先进的机器学习算法。

如果还没有 Azure 订阅，可以在开始前[创建一个帐户](https://azure.microsoft.com/free/)。

> [!NOTE]
> Azure SQL 数据库中的机器学习服务（使用 R）当前为公共预览版。 [注册预览版](sql-database-machine-learning-services-overview.md#signup)。

## <a name="prerequisites"></a>先决条件

若要在这些练习中运行示例代码，必须先有一个启用机器学习服务（使用 R）的 SQL 数据库。 在发布公共预览版期间，Microsoft 会将你加入该版本并为你的现有数据库或新数据库启用机器学习。 执行[注册预览版](sql-database-machine-learning-services-overview.md#signup)中的步骤。

可以使用任何数据库管理或查询工具连接到 SQL 数据库并运行 R 脚本，前提是该工具能够连接到 SQL 数据库并运行 T-SQL 查询或存储过程。 本快速入门使用 [SQL Server Management Studio](sql-database-connect-query-ssms.md)。

若要进行[添加包](#add-package)练习，则还需在本地计算机上安装 [R](https://www.r-project.org/) 和 [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/)。

本快速入门还需要配置服务器级防火墙规则。 有关说明如何执行此操作的快速入门，请参阅[创建服务器级防火墙规则](sql-database-server-level-firewall-rule.md)。

## <a name="verify-r-exists"></a>验证 R 是否存在

可以确认是否为 SQL 数据库启用了机器学习服务（使用 R）。 请按照以下步骤进行操作。

1. 打开 SQL Server Management Studio，连接到 SQL 数据库。 有关如何进行连接的详细信息，请参阅[快速入门：使用 SQL Server Management Studio 连接和查询 Azure SQL 数据库](sql-database-connect-query-ssms.md)。

1. 运行以下代码。 

    ```sql
    EXECUTE sp_execute_external_script
    @language =N'R',
    @script=N'print(31 + 11)';
    GO
    ```
    如果一切正常，则会看到如下所示的结果消息。

    ```text
    STDOUT message(s) from external script: 
    42
    ```

1. 如果出现错误，则可能是因为尚未为 SQL 数据库启用机器学习服务（使用 R）的公共预览版。 了解如何注册获取上述公共预览版。

## <a name="grant-permissions"></a>授予权限

如果你是管理员，可以自动运行外部代码。 必须向其他所有人授予权限。

在运行该命令之前，需将 `<username>` 替换为有效的数据库用户登录名。

```sql
GRANT EXECUTE ANY EXTERNAL SCRIPT TO <username>
```

## <a name="basic-r-interaction"></a>基本的 R 交互

可以通过两种方式在 SQL 数据库中运行 R 代码：

+ 将 R 脚本作为系统存储过程 [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) 的参数添加。
+ 从[远程 R 客户端](https://docs.microsoft.com/sql/advanced-analytics/r/set-up-a-data-science-client)连接到 SQL 数据库，并使用 SQL 数据库作为计算上下文来执行代码。

以下练习着重于第一个交互模型：如何将 R 代码传递给存储过程。

1. 运行简单的脚本，看看 R 脚本如何在 SQL 数据库中执行。

    ```sql
    EXECUTE sp_execute_external_script
    @language = N'R',
    @script = N'
    a <- 1
    b <- 2
    c <- a/b
    d <- a*b
    print(c, d)'
    ```

2. 假设一切设置正确，则会计算出正确的结果，且 R 的 `print` 函数会将结果返回到“消息”窗口。

    **结果**

    ```text
    STDOUT message(s) from external script: 
    0.5 2
    ```

    虽然在测试代码时获取 **stdout** 消息很有用，但更多时候需要返回表格形式的结果，方便其在应用程序中使用或将其写入表中。 有关详细信息，请参阅下面的输入和输出部分。

请记住，`@script` 参数中的所有内容必须是有效的 R 代码。

## <a name="inputs-and-outputs"></a>输入和输出

默认情况下，[sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) 接受单个输入数据集，该数据集通常以有效 SQL 查询的形式提供。 其他类型的输入可以作为 SQL 变量传递。

存储过程将单个 R 数据帧作为输出返回，但你也可以将标量和模型作为变量输出。 例如，可以将训练的模型作为二进制变量输出，然后将其传递给 T-SQL INSERT 语句，以便将该模型写入到表中。 也可生成绘图（二进制格式）或标量（单个值，例如日期和时间、训练模型所用的时间，等等）。

现在，让我们单纯查看 sp_execute_external_script 的默认输入和输出变量：`InputDataSet` 和 `OutputDataSet`。

1. 通过运行以下 T-SQL 语句创建一个包含测试数据的小表：

    ```sql
    CREATE TABLE RTestData (col1 INT NOT NULL)
    INSERT INTO RTestData VALUES (1);
    INSERT INTO RTestData VALUES (10);
    INSERT INTO RTestData VALUES (100);
    GO
    ```

    表创建完以后，请使用以下语句来查询表：
  
    ```sql
    SELECT * FROM RTestData
    ```

    **结果**

    ![RTestData 表的内容](./media/sql-database-connect-query-r/select-rtestdata.png)

2. 可以从表中获取数据，作为 R 脚本的输入。 请运行下面的语句。 该语句从表中获取数据，在 R 运行时中往返一次后，返回列名为 *NewColName* 的值。

    查询返回的数据传递到 R 运行时，后者再将数据作为数据帧返回到 SQL 数据库。 WITH RESULT SETS 子句为 SQL 数据库定义返回的数据表的架构。

    ```sql
    EXECUTE sp_execute_external_script
        @language = N'R'
        , @script = N'OutputDataSet <- InputDataSet;'
        , @input_data_1 = N'SELECT * FROM RTestData;'
    WITH RESULT SETS (([NewColName] INT NOT NULL));
    ```

    **结果**

    ![可以从表返回数据的 R 脚本的输出](./media/sql-database-connect-query-r/r-output-rtestdata.png)

3. 让我们更改输入或输出变量的名称。 上面的脚本使用了默认的输入和输出变量名称：_InputDataSet_ 和 _OutputDataSet_。 若要定义与 _InputDatSet_ 相关联的输入数据，请使用 *@input_data_1* 变量。

    在此脚本中，存储过程的输出和输入变量的名称已更改为 *SQL_out* 和 *SQL_in*：

    ```sql
    EXECUTE sp_execute_external_script
      @language = N'R'
      , @script = N' SQL_out <- SQL_in;'
      , @input_data_1 = N' SELECT 12 as Col;'
      , @input_data_1_name  = N'SQL_in'
      , @output_data_1_name =  N'SQL_out'
      WITH RESULT SETS (([NewColName] INT NOT NULL));
    ```

    请注意，R 区分大小写，因此 `@input_data_1_name` 和 `@output_data_1_name` 中的输入和输出变量的大小写必须与 `@script` 中 R 代码的相应变量的大小写匹配。 

    另外，参数顺序很重要。 必须先指定必需参数 *@input_data_1* 和 *@output_data_1*，然后才能使用可选参数 *@input_data_1_name* 和 *@output_data_1_name*。

    只能将一个输入数据集作为参数传递，并且只能返回一个数据集。 但是，可以从 R 代码内部调用其他数据集，并且可以返回除数据集之外的其他类型的输出。 也可向任何参数添加 OUTPUT 关键字，让该参数随结果一起返回。 

    `WITH RESULT SETS` 语句为 SQL 数据库中使用的数据定义架构。 需为从 R 返回的每个列提供与 SQL 兼容的数据类型。也可使用架构定义来提供新的列名，因为不需使用来自 R 数据帧的列名。

4. 也可使用 R 脚本来生成值，将 _@input_data_1_ 中的输入查询字符串留空。

    ```sql
    EXECUTE sp_execute_external_script
        @language = N'R'
        , @script = N' mytextvariable <- c("hello", " ", "world");
            OutputDataSet <- as.data.frame(mytextvariable);'
        , @input_data_1 = N''
    WITH RESULT SETS (([Col1] CHAR(20) NOT NULL));
    ```

    **结果**

    ![使用 @script 作为输入的查询结果](./media/sql-database-connect-query-r/r-data-generated-output.png)

## <a name="check-r-version"></a>检查 R 版本

若要查看在 SQL 数据库中安装的 R 的版本，请执行以下操作：

1. 在 SQL 数据库上运行下面的脚本。

    ```SQL
    EXECUTE sp_execute_external_script
    @language =N'R',
    @script=N'print(version)';
    GO
    ```

2. R 的 `print` 函数将版本返回到“消息”窗口。 在下面的示例输出中，可以看到此示例中的 SQL 数据库已安装 R 版本 3.4.4。

    **结果**

    ```text
    STDOUT message(s) from external script:
                   _
    platform       x86_64-w64-mingw32
    arch           x86_64
    os             mingw32
    system         x86_64, mingw32
    status
    major          3
    minor          4.4
    year           2018
    month          03
    day            15
    svn rev        74408
    language       R
    version.string R version 3.4.4 (2018-03-15)
    nickname       Someone to Lean On
    ```

## <a name="list-r-packages"></a>列出 R 包

Microsoft 提供许多预安装在 SQL 数据库的机器学习服务中的 R 包。 若要查看已安装 R 包的列表（包括版本、依赖项、许可证和库路径信息），请执行下面的步骤。 若要添加其他包，请参阅[添加包](#add-package)部分。

1. 在 SQL 数据库上运行下面的脚本。

    ```SQL
    EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
    OutputDataSet <- data.frame(installed.packages()[,c("Package", "Version", "Depends", "License", "LibPath")]);'
    WITH result sets((Package NVARCHAR(255), Version NVARCHAR(100), Depends NVARCHAR(4000)
        , License NVARCHAR(1000), LibPath NVARCHAR(2000)));
    ```

2. 输出来自 R 中的 `installed.packages()`，以结果集的形式返回。

    **结果**

    ![R 中的已安装包](./media/sql-database-connect-query-r/r-installed-packages.png)

## <a name="create-a-predictive-model"></a>创建预测模型

可以使用 R 来训练模型，然后将模型保存在 SQL 数据库的表中。 在此练习中，将训练一个简单的回归模型，用于根据速度预测汽车的制动距离。 将使用 R 随附的 `cars` 数据集，因为它小且易于理解。

1. 首先，创建一个保存训练数据的表。

    ```sql
    CREATE TABLE dbo.CarSpeed (speed INT NOT NULL, distance INT NOT NULL)
    GO
    INSERT INTO dbo.CarSpeed (speed, distance)
    EXEC sp_execute_external_script
    @language = N'R'
        , @script = N'car_speed <- cars;'
        , @input_data_1 = N''
        , @output_data_1_name = N'car_speed'
    GO
    ```

    R 运行时随附许多数据集，有大有小。 若要获取在 R 中安装的数据集的列表，请通过 R 命令提示符键入 `library(help="datasets")`。

2. 创建回归模型。 汽车速度数据包含两个列，即 `dist` 和 `speed`，均为数值。 对某些速度进行了多次观察。 将通过此数据创建一个线性回归模型，用于描述汽车速度与汽车制动所需距离之间的某种关系。

    线性模型的要求很简单：

    - 定义一个公式，用于描述因变量 `speed` 与自变量 `distance` 之间的关系。

    - 提供用于训练模型的输入数据。

    > [!TIP]
    > 如果需要补习线性模型方面的内容，建议使用以下教程，它介绍了使用 rxLinMod 进行模型拟合的过程：[拟合线性模型](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-linear-model)

    若要生成模型，请在 R 代码中定义公式，然后以输入参数的方式传递数据。

    ```sql
    DROP PROCEDURE IF EXISTS generate_linear_model;
    GO
    CREATE PROCEDURE generate_linear_model
    AS
    BEGIN
        EXEC sp_execute_external_script
        @language = N'R'
        , @script = N'lrmodel <- rxLinMod(formula = distance ~ speed, data = CarsData);
            trained_model <- data.frame(payload = as.raw(serialize(lrmodel, connection=NULL)));'
        , @input_data_1 = N'SELECT [speed], [distance] FROM CarSpeed'
        , @input_data_1_name = N'CarsData'
        , @output_data_1_name = N'trained_model'
        WITH RESULT SETS ((model VARBINARY(max)));
    END;
    GO
    ```

    rxLinMod 的第一个参数是 *formula* 参数，它将距离定义为速度的因变量。 输入数据存储在由 SQL 查询填充的 `CarsData` 变量中。 如果不为输入数据分配特定的名称，则默认变量名称为 _InputDataSet_。

2. 接下来创建一个存储模型的表，这样就能重新训练或使用它，以便进行预测。 创建模型的 R 包的输出通常为**二进制对象**。 因此，此表必须提供一个 **VARBINARY(max)** 类型的列。

    ```sql
    CREATE TABLE dbo.stopping_distance_models (
        model_name VARCHAR(30) NOT NULL DEFAULT('default model') PRIMARY KEY
        , model VARBINARY(max) NOT NULL
    );
    ```
3. 若要保存模型，请运行以下 Transact-SQL 语句以调用存储过程，在生成模型后再将其保存到表中。

    ```sql
    INSERT INTO dbo.stopping_distance_models (model)
    EXEC generate_linear_model;
    ```

    请注意，如果再次运行该代码，则会出现以下错误：

    ```text
    Violation of PRIMARY KEY constraint...Cannot insert duplicate key in object dbo.stopping_distance_models
    ```

    若要避免此错误，一个选择是更新每个新模型的名称。 例如，可以将名称更改为更具说明性的名称，并且包括模型类型、创建日期等。

    ```sql
    UPDATE dbo.stopping_distance_models
    SET model_name = 'rxLinMod ' + FORMAT(GETDATE(), 'yyyy.MM.HH.mm', 'en-gb')
    WHERE model_name = 'default model'
    ```

4. 通常情况下，存储过程 [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) 的 R 输出仅限单个数据帧。

    但是，可以返回除数据帧之外的其他类型的输出，例如标量。

    例如，假设你需要训练一个模型，但需要立即查看模型的系数表。 可以将系数表作为主结果集创建，然后在 SQL 变量中输出训练的模型。 可以通过调用该变量来立即重用模型，也可以将模型保存到表中，如下所示。

    ```sql
    DECLARE @model VARBINARY(max), @modelname VARCHAR(30)
    EXEC sp_execute_external_script
        @language = N'R'
        , @script = N'
            speedmodel <- rxLinMod(distance ~ speed, CarsData)
            modelbin <- serialize(speedmodel, NULL)
            OutputDataSet <- data.frame(coefficients(speedmodel));'
        , @input_data_1 = N'SELECT [speed], [distance] FROM CarSpeed'
        , @input_data_1_name = N'CarsData'
        , @params = N'@modelbin varbinary(max) OUTPUT'
        , @modelbin = @model OUTPUT
        WITH RESULT SETS (([Coefficient] FLOAT NOT NULL));

    -- Save the generated model
    INSERT INTO dbo.stopping_distance_models(model_name, model)
    VALUES ('latest model', @model)
    ```

    **结果**

    ![具有额外输出的已训练模型](./media/sql-database-connect-query-r/r-train-model-with-additional-output.png)

## <a name="predict"></a>预测

请使用在上一部分创建的模型对根据新数据进行的预测评分。 若要使用新数据进行评分，请从表中获取一个训练的模型，然后调用进行预测所需的新数据集。 评分是一个术语，有时在数据科学中用来表示根据馈送到已训练模型中的新数据生成预测、概率或其他值。

1. 首先，使用新的速度数据创建一个表。 你是否注意到，原始训练数据的最高速度是 25 英里/小时？ 这是因为原始数据基于 1920 年的一次试验！ 你可能想知道，如果一辆上世纪 20 年代的汽车可以达到 60 mph 甚至 100 mph 的速度，其制动时间会是多长？ 若要回答此问题，必须提供一些新的速度值。

    ```sql
    CREATE TABLE dbo.NewCarSpeed(speed INT NOT NULL,
        distance INT NULL
    )
    GO
    INSERT dbo.NewCarSpeed(speed)
    VALUES (40), (50), (60), (70), (80), (90), (100)
    ```

    在以下示例中，由于模型基于 **RevoScaleR** 包中提供的 **rxLinMod** 算法，因此调用 [rxPredict](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxpredict) 函数而不是 R 的泛型 `predict` 函数。

    ```sql
    DECLARE @speedmodel varbinary(max) = 
        (SELECT model FROM dbo.stopping_distance_models WHERE model_name = 'latest model');

    EXEC sp_execute_external_script
        @language = N'R'
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
    WITH RESULT SETS ((new_speed INT, predicted_distance INT));
    ```

    上述脚本执行以下步骤：

    + 使用 SELECT 语句从表中获取单个模型，然后将其作为输入参数传递。

    + 从表中检索模型以后，在该模型上调用 `unserialize` 函数。

        > [!TIP] 
        > 另请查看由 RevoScaleR 提供的新的[序列化函数](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxserializemodel)，这些函数支持实时评分。
    + 将带有适当参数的 `rxPredict` 函数应用到模型，并提供新的输入数据。

    + 在示例中，`str` 函数是在测试阶段添加的，用于检查从 R 返回的数据的架构。可以稍后删除该语句。

    + 在 R 脚本中使用的列名不一定传递到存储过程输出。 在这里，我们使用了 WITH RESULTS 子句来定义一些新的列名。

    **结果**

    ![预测制动距离的结果集](./media/sql-database-connect-query-r/r-predict-stopping-distance-resultset.png)

    也可根据存储的模型使用 [Transact-SQL 中的 PREDICT](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql) 生成预测的值或分数。

<a name="add-package"></a>

## <a name="add-a-package"></a>添加包

如需使用尚未安装在 SQL 数据库中的包，可以使用 [sqlmlutils](https://github.com/Microsoft/sqlmlutils) 来安装它。 请按以下步骤安装该包。

1. 将最新的 **sqlmlutils** zip 文件从 [github.com/Microsoft/sqlmlutils/tree/master/R/dist](https://github.com/Microsoft/sqlmlutils/tree/master/R/dist) 下载到本地计算机。 不需将文件解压缩。

1. 如果尚未安装 R，请从 [www.r-project.org](https://www.r-project.org/) 下载 R 并将其安装在本地计算机上。 R 适用于 Windows、MacOS 和 Linux。 在此示例中，我们使用的是 Windows。

1. 首先安装 **RODBCext** 包，这是使用 **sqlmlutils** 的先决条件。 **RODBCext** 也安装依赖项：**RODBC** 包。 打开一个**命令提示符**并运行以下命令：

    ```
    R -e "install.packages('RODBCext', repos='https://cran.microsoft.com')"
    ```

    如果收到错误“'R' 未识别为内部或外部命令、可运行程序或批处理文件”，则可能意味着 R.exe 的路径未包括在 Windows 的 **PATH** 环境变量中。 在运行此命令之前，可以将目录添加到该环境变量，也可以导航到命令提示符中的目录（例如 `cd C:\Program Files\R\R-3.5.1\bin`）。

1. 使用 **R CMD INSTALL** 命令安装 **sqlmlutils**。 指定已将 zip 文件下载到其中的目录的路径，以及 zip 文件的名称。 例如：

    ```
    R CMD INSTALL C:\Users\youruser\Downloads\sqlmlutils_0.5.0.zip
    ```

    获得的输出应如下所示：

    ```text
    In R CMD INSTALL
    * installing to library 'C:/Users/youruser/Documents/R/win-library/3.5'
    package 'sqlmlutils' successfully unpacked and MD5 sums checked
    ```

1. 在此示例中，将使用 RStudio Desktop 作为 IDE。 如果愿意，也可以使用其他 IDE。 请从 [www.rstudio.com/products/rstudio/download/](https://www.rstudio.com/products/rstudio/download/) 下载并安装 RStudio Desktop（如果尚未安装 RStudio）。

1. 打开 **RStudio** 并创建新的 **R 脚本**文件。 请使用以下 R 代码通过 sqlmlutils 来安装包。 在以下示例中，将安装可以格式化和内插字符串的 [glue](https://cran.r-project.org/web/packages/glue/) 包。

    ```R
    library(sqlmlutils) 
    connection <- connectionInfo(server= "yourserver.database.windows.net", 
        database = "yourdatabase", uid = "yoursqluser", pwd = "yoursqlpassword")
    sql_install.packages(connectionString = connection, pkgs = "glue", verbose = TRUE, scope = "PUBLIC")
    ```

    > [!NOTE]
    > **scope** 可以是 **PUBLIC** 或 **PRIVATE**。 公共范围适用于数据库管理员安装供所有用户使用的包。 如果选择专用范围，则包只能供安装该包的用户使用。 如果不指定范围，则默认范围为 **PRIVATE**。

1. 现在，验证 **glue** 包是否已安装。

    ```R
    r<-sql_installed.packages(connectionString = connection, fields=c("Package", "LibPath", "Attributes", "Scope"))
    View(r)
    ```

    **结果**

    ![RTestData 表的内容](./media/sql-database-connect-query-r/r-verify-package-install.png)


1. 包安装好以后，即可在 R 脚本中通过 **sp_execute_external_script** 来使用它。 打开 **SQL Server Management Studio**，连接到 SQL 数据库。 运行以下脚本：

    ```sql
    EXEC sp_execute_external_script @language = N'R'
    , @script = N'
    library(glue)

    name <- "Fred"
    age <- 50
    anniversary <- as.Date("2020-06-14")
    text <- glue(''My name is {name}, '',
        ''my age next year is {age + 1}, '',
        ''my anniversary is {format(anniversary, "%A, %B %d, %Y")}.'')
    print(text)
    ';
    ```

    此时会在“消息”选项卡中看到以下结果。

    **结果**

    ```text
    STDOUT message(s) from external script:
    My name is Fred, my age next year is 51, my anniversary is Sunday, June 14, 2020.
    ```

1. 若要删除包，请在本地计算机的 **RStudio** 中运行以下 R 脚本。

    ```R
    library(sqlmlutils) 
    connection <- connectionInfo(server= "yourserver.database.windows.net", 
        database = "yourdatabase", uid = "yoursqluser", pwd = "yoursqlpassword")
    sql_remove.packages(connectionString = connection, pkgs = "glue", scope = "PUBLIC") 
    ```

> [!NOTE]
> 若要将 R 包安装到 SQL 数据库，另一种方式是使用 [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql) 通过字节流来上传 R 包。

## <a name="next-steps"></a>后续步骤

若要详细了解机器学习服务，请参阅以下文章。 虽然这些文章中有一些是针对 SQL Server 的，但大多数信息也适用于 Azure SQL 数据库中的机器学习服务（使用 R）。

- [Azure SQL 数据库机器学习服务（使用 R）](sql-database-machine-learning-services-overview.md)
- [SQL Server 机器学习服务](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning)
- [教程：了解在 SQL Server 中使用 R 进行数据库内分析](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-in-database-r-for-sql-developers)
- [End-to-end data science walkthrough for R and SQL Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/walkthrough-data-science-end-to-end-walkthrough)（适用于 R 和 SQL Server 的端到端数据科学演练）
- [教程：将 RevoScaleR R 函数与 SQL Server 数据配合使用](https://docs.microsoft.com/sql/advanced-analytics/tutorials/deepdive-data-science-deep-dive-using-the-revoscaler-packages)
