---
title: 在 Visual Studio Code 中使用 Python、R 和 C# 开发用于 Azure Data Lake Analytics 的 U-SQL
description: 了解如何使用 Python、R 和 C# 的隐藏代码来提交 Azure Data Lake 中的作业。
services: data-lake-analytics
ms.service: data-lake-analytics
author: jejiang
ms.author: jejiang
manager: kfile
editor: jasonwhowell
ms.topic: conceptual
ms.date: 11/22/2017
ms.openlocfilehash: 171aef186fd681adf9b3d92deb8691c852ea1038
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34624901"
---
# <a name="develop-u-sql-with-python-r-and-c-for-azure-data-lake-analytics-in-visual-studio-code"></a>在 Visual Studio Code 中使用 Python、R 和 C# 开发用于 Azure Data Lake Analytics 的 U-SQL
了解如何使用 Visual Studio Code (VSCode) 来编写 U-SQL 的 Python、R 和 C# 隐藏代码，并将作业提交到 Azure Data Lake 服务。 有关 VSCode 的 Azure Data Lake 工具的详细信息，请参阅[使用针对 Visual Studio Code 的 Azure Data Lake 工具](data-lake-analytics-data-lake-tools-for-vscode.md)。

在编写代码隐藏自定义代码之前，需要在 VSCode 中打开文件夹或工作区。


## <a name="prerequisites-for-python-and-r"></a>Python 和 R 的先决条件
注册 ADL 帐户的 Python 和 R 扩展程序集。 
1. 在门户中打开帐户。
   - 选择“概述”。 
   - 单击“示例脚本”。
2. 单击“更多”。
3. 选择“安装 U-SQL 扩展”。 
4. 安装 U-SQL 扩展后，将显示确认消息。 

  ![设置 python 和 R 环境](./media/data-lake-analytics-data-lake-tools-for-vscode/setup-the-enrionment-for-python-and-r.png)

  > [!Note]
  > 为了获得 Python 和 R 语言服务的最佳体验，请安装 VSCode Python 和 R 扩展。 

## <a name="develop-python-file"></a>开发 Python 文件
1. 单击工作区中的“新文件”。
2. 在 U-SQL 中编写代码。 下面就是一个代码示例。
    ```U-SQL
    REFERENCE ASSEMBLY [ExtPython];
    @t  = 
        SELECT * FROM 
        (VALUES
            ("D1","T1","A1","@foo Hello World @bar"),
            ("D2","T2","A2","@baz Hello World @beer")
        ) AS 
            D( date, time, author, tweet );

    @m  =
        REDUCE @t ON date
        PRODUCE date string, mentions string
        USING new Extension.Python.Reducer("pythonSample.usql.py", pyVersion : "3.5.1");

    OUTPUT @m
        TO "/tweetmentions.csv"
        USING Outputters.Csv();
    ```
    
3. 右键单击脚本文件，并选择“ADL: 生成 Python 代码隐藏文件”。 
4. xxx.usql.py 文件将在工作文件夹中生成。 在 Python 文件中编写代码。 下面就是一个代码示例。

    ```Python
    def get_mentions(tweet):
        return ';'.join( ( w[1:] for w in tweet.split() if w[0]=='@' ) )

    def usqlml_main(df):
        del df['time']
        del df['author']
        df['mentions'] = df.tweet.apply(get_mentions)
        del df['tweet']
        return df
    ```
5. 右键单击 USQL 文件，可以单击“编译脚本”或“提交作业”以运行作业。

## <a name="develop-r-file"></a>开发 R 文件
1. 单击工作区中的“新文件”。
2. 在 U-SQL 文件中编写代码。 下面就是一个代码示例。
    ```U-SQL
    DEPLOY RESOURCE @"/usqlext/samples/R/my_model_LM_Iris.rda";
    DECLARE @IrisData string = @"/usqlext/samples/R/iris.csv";
    DECLARE @OutputFilePredictions string = @"/my/R/Output/LMPredictionsIris.txt";
    DECLARE @PartitionCount int = 10;

    @InputData =
        EXTRACT SepalLength double,
                SepalWidth double,
                PetalLength double,
                PetalWidth double,
                Species string
        FROM @IrisData
        USING Extractors.Csv();

    @ExtendedData =
        SELECT Extension.R.RandomNumberGenerator.GetRandomNumber(@PartitionCount) AS Par,
            SepalLength,
            SepalWidth,
            PetalLength,
            PetalWidth
        FROM @InputData;

    // Predict Species

    @RScriptOutput =
        REDUCE @ExtendedData
        ON Par
        PRODUCE Par,
                fit double,
                lwr double,
                upr double
        READONLY Par
        USING new Extension.R.Reducer(scriptFile : "RClusterRun.usql.R", rReturnType : "dataframe", stringsAsFactors : false);
    OUTPUT @RScriptOutput
    TO @OutputFilePredictions
    USING Outputters.Tsv();
    ```
3. 右键单击 USQL 文件，并选择“ADL: 生成 R 代码隐藏文件”。 
4. xx.usql.r 文件将在工作文件夹中生成。 在 R 文件中编写代码。 下面就是一个代码示例。

    ```R
    load("my_model_LM_Iris.rda")
    outputToUSQL=data.frame(predict(lm.fit, inputFromUSQL, interval="confidence"))
    ```
5. 右键单击 USQL 文件，可以单击“编译脚本”或“提交作业”以运行作业。

## <a name="develop-c-file"></a>开发 C# 文件
代码隐藏文件是与单个 U-SQL 脚本关联的 C# 文件。 可在代码隐藏文件中定义 UDO、UDA、UDT 和 UDF 专用的脚本。 无需事先注册程序集，即可直接在脚本中使用 UDO、UDA、UDT 和 UDF。 代码隐藏文件放置在其对等 U-SQL 脚本文件所在的文件夹中。 如果脚本名为 xxx.usql，则代码隐藏文件命名为 xxx.usql.cs。 如果手动删除代码隐藏文件，会对关联的 U-SQL 脚本禁用代码隐藏功能。 有关为 U-SQL 脚本编写自定义代码的详细信息，请参阅[在 U-SQL 中编写和使用自定义代码：用户定义的函数]( https://blogs.msdn.microsoft.com/visualstudio/2015/10/28/writing-and-using-custom-code-in-u-sql-user-defined-functions/)。

1. 单击工作区中的“新文件”。
2. 在 U-SQL 文件中编写代码。 下面就是一个代码示例。
    ```U-SQL
    @a = 
        EXTRACT 
            Iid int,
        Starts DateTime,
        Region string,
        Query string,
        DwellTime int,
        Results string,
        ClickedUrls string 
        FROM @"/Samples/Data/SearchLog.tsv" 
        USING Extractors.Tsv();

    @d =
        SELECT DISTINCT Region 
        FROM @a;

    @d1 = 
        PROCESS @d
        PRODUCE 
            Region string,
        Mkt string
        USING new USQLApplication_codebehind.MyProcessor();

    OUTPUT @d1 
        TO @"/output/SearchLogtest.txt" 
        USING Outputters.Tsv();
    ```
3. 右键单击 USQL 文件，并选择“ADL: 生成 CS 代码隐藏文件”。 
4. xxx.usql.cs 文件将在工作文件夹中生成。 在 CS 文件中编写代码。 下面就是一个代码示例。

    ```CS
    namespace USQLApplication_codebehind
    {
        [SqlUserDefinedProcessor]

        public class MyProcessor : IProcessor
        {
            public override IRow Process(IRow input, IUpdatableRow output)
            {
                output.Set(0, input.Get<string>(0));
                output.Set(1, input.Get<string>(0));
                return output.AsReadOnly();
            } 
        }
    }
    ```
5. 右键单击 USQL 文件，可以单击“编译脚本”或“提交作业”以运行作业。

## <a name="next-steps"></a>后续步骤
* [使用用于 Visual Studio Code 的 Azure Data Lake 工具](data-lake-analytics-data-lake-tools-for-vscode.md)
* [使用 Visual Studio Code 进行 U-SQL 本地运行和本地调试](data-lake-tools-for-vscode-local-run-and-debug.md)
* [通过 PowerShell 实现 Data Lake Analytics 入门](data-lake-analytics-get-started-powershell.md)
* [通过 Azure 门户实现 Data Lake Analytics 入门](data-lake-analytics-get-started-portal.md)
* [使用适用于 Visual Studio 的工具开发 U-SQL 应用程序](data-lake-analytics-data-lake-tools-get-started.md)
* [使用 Data Lake Analytics(U-SQL) 目录](data-lake-analytics-use-u-sql-catalog.md)
