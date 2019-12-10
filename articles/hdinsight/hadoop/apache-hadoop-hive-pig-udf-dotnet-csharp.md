---
title: C#，Apache Hive & Apache Pig on Apache Hadoop-Azure HDInsight
description: 了解在 Azure HDInsight 中如何将 C# 用户定义函数 (UDF) 与 Apache Hive 和 Apache Pig 流式处理配合使用。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 9ef9eada9b9aec50642a8bf357edab0677868817
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2019
ms.locfileid: "74949383"
---
# <a name="use-c-user-defined-functions-with-apache-hive-and-apache-pig-on-apache-hadoop-in-hdinsight"></a>将C#用户定义函数与 HDInsight 中 Apache Hadoop 上的 Apache Hive 和 Apache Pig 配合使用

了解如何将用户C#定义的函数（UDF）与[Apache Hive](https://hive.apache.org)和[Apache Pig](https://pig.apache.org) on HDInsight 配合使用。

> [!IMPORTANT]
> 本文档中的步骤适用于基于 Linux 的 HDInsight 群集。 Linux 是 HDInsight 3.4 或更高版本上使用的唯一操作系统。 有关详细信息，请参阅 [HDInsight 组件版本控制](../hdinsight-component-versioning.md)。

Hive 和 Pig 都可以将数据传递到外部应用程序以进行处理。 此过程称为_流式处理_。 使用 .NET 应用程序时，数据将传递到 STDIN 上的应用程序，该应用程序也会在 STDOUT 上返回结果。 若要从 STDIN 和 STDOUT 读取和写入数据，可以使用控制台应用程序中的 `Console.ReadLine()` 和 `Console.WriteLine()`。

## <a name="prerequisites"></a>必备组件

* 熟悉编写和生成面向 .NET Framework 4.5 的 C# 代码。

    使用任何需要的 IDE。 建议[Visual Studio](https://www.visualstudio.com/vs)或[Visual Studio Code](https://code.visualstudio.com/)。 本文档中的步骤使用 Visual Studio 2019。

* 将 .exe 文件上传到群集以及运行 Pig 和 Hive 作业的方法。 建议[Data Lake 用于 Visual Studio 的工具](../../data-lake-analytics/data-lake-analytics-data-lake-tools-install.md)、 [Azure PowerShell](/powershell/azure)和[Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。 本文档中的各个步骤都使用针对 Visual Studio 的 Data Lake 工具上传文件和运行 Hive 查询示例。

    有关运行 Hive 查询的其他方法的信息，请参阅[Azure HDInsight 上的 Apache Hive 和 HiveQL 是什么？](hdinsight-use-hive.md)。

* HDInsight 群集上的 Hadoop。 有关创建群集的详细信息，请参阅[创建 HDInsight 群集](../hdinsight-hadoop-provision-linux-clusters.md)。

## <a name="net-on-hdinsight"></a>HDInsight 上的 .NET

*基于 Linux 的 HDInsight* 群集使用 [Mono (https://mono-project.com)](https://mono-project.com) 运行 .NET 应用程序。 HDInsight 版本 3.6 附带了 Mono 版本 4.2.1。

有关 Mono 与 .NET Framework 版本的兼容性的详细信息，请参阅 [Mono 兼容性](https://www.mono-project.com/docs/about-mono/compatibility/)。

有关 HDInsight 版本随附的 .NET Framework 和 Mono 版本的详细信息，请参阅[hdinsight 组件版本](../hdinsight-component-versioning.md)。

## <a name="create-the-c-projects"></a>创建 C\# 项目

以下部分介绍如何在 Visual Studio 中C#为 Apache Hive Udf 和 APACHE Pig udf 创建项目。

### <a name="apache-hive-udf"></a>Apache Hive UDF

为 Apache Hive UDF C#创建项目：

1. 启动 Visual Studio。

2. 选择“创建新项目”。

3. 在 "新建**项目**" 窗口中，选择 "**控制台应用（.NET Framework）** " 模板（ C#版本）。 然后，选择“下一步”。

4. 在 "**配置新项目**" 窗口中，输入 " *Hivecsharp.exe*" 的**项目名称**，然后导航到或创建要在其中保存新项目的**位置**。 然后选择“创建”。

5. 在 Visual Studio IDE 中，将*Program.cs*的内容替换为以下代码：

    ```csharp
    using System;
    using System.Security.Cryptography;
    using System.Text;
    using System.Threading.Tasks;

    namespace HiveCSharp
    {
        class Program
        {
            static void Main(string[] args)
            {
                string line;
                // Read stdin in a loop
                while ((line = Console.ReadLine()) != null)
                {
                    // Parse the string, trimming line feeds
                    // and splitting fields at tabs
                    line = line.TrimEnd('\n');
                    string[] field = line.Split('\t');
                    string phoneLabel = field[1] + ' ' + field[2];
                    // Emit new data to stdout, delimited by tabs
                    Console.WriteLine("{0}\t{1}\t{2}", field[0], phoneLabel, GetMD5Hash(phoneLabel));
                }
            }
            /// <summary>
            /// Returns an MD5 hash for the given string
            /// </summary>
            /// <param name="input">string value</param>
            /// <returns>an MD5 hash</returns>
            static string GetMD5Hash(string input)
            {
                // Step 1, calculate MD5 hash from input
                MD5 md5 = System.Security.Cryptography.MD5.Create();
                byte[] inputBytes = System.Text.Encoding.ASCII.GetBytes(input);
                byte[] hash = md5.ComputeHash(inputBytes);

                // Step 2, convert byte array to hex string
                StringBuilder sb = new StringBuilder();
                for (int i = 0; i < hash.Length; i++)
                {
                    sb.Append(hash[i].ToString("x2"));
                }
                return sb.ToString();
            }
        }
    }
    ```

6. 从菜单栏中，选择 " > **生成**" "生成" "生成**解决方案**" 以生成项目。

7. 关闭解决方案。

### <a name="apache-pig-udf"></a>Apache Pig UDF

为 Apache Hive UDF C#创建项目：

1. 打开 Visual Studio。

2. 在 "**开始**" 窗口中，选择 "**创建新项目**"。

3. 在 "新建**项目**" 窗口中，选择 "**控制台应用（.NET Framework）** " 模板（ C#版本）。 然后，选择“下一步”。

4. 在 "**配置新项目**" 窗口中，输入 " *Pigudf.exe*" 的**项目名称**，然后前往或创建用于保存新项目的**位置**。 然后选择“创建”。

5. 在 Visual Studio IDE 中，将*Program.cs*的内容替换为以下代码：

    ```csharp
    using System;

    namespace PigUDF
    {
        class Program
        {
            static void Main(string[] args)
            {
                string line;
                // Read stdin in a loop
                while ((line = Console.ReadLine()) != null)
                {
                    // Fix formatting on lines that begin with an exception
                    if(line.StartsWith("java.lang.Exception"))
                    {
                        // Trim the error info off the beginning and add a note to the end of the line
                        line = line.Remove(0, 21) + " - java.lang.Exception";
                    }
                    // Split the fields apart at tab characters
                    string[] field = line.Split('\t');
                    // Put fields back together for writing
                    Console.WriteLine(String.Join("\t",field));
                }
            }
        }
    }
    ```

    此代码分析发送自 Pig 的行，并对以 `java.lang.Exception` 开头的行重新设置格式。

6. 从菜单栏中，选择 "**生成**" > **生成解决方案**"以生成项目。

7. 使解决方案保持打开状态。

## <a name="upload-to-storage"></a>上传到存储

接下来，将 Hive 和 Pig UDF 应用程序上传到 HDInsight 群集上的存储。

1. 在 Visual Studio 中，导航到 "**查看** > "**服务器资源管理器**"。

1. 在**服务器资源管理器**中，右键单击 " **Azure**"，选择 "**连接到 Microsoft Azure 订阅**"，然后完成登录过程。

1. 展开要将此应用程序部署到的 HDInsight 群集。 将列出带有文本“（默认存储帐户）”的条目。

    ![默认存储帐户，HDInsight 群集，服务器资源管理器](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/hdinsight-storage-account.png)

    * 如果此条目可以展开，则使用**Azure 存储帐户**作为群集的默认存储。 要查看该群集的默认存储上的文件，请展开该条目，并双击“（默认容器）”。

    * 如果无法展开此项，则使用**Azure Data Lake Storage**作为群集的默认存储。 若要查看该群集的默认存储上的文件，请双击“（默认存储帐户）”条目。

1. 若要上传 .exe 文件，请使用以下方法之一：

    * 如果使用的是**Azure 存储帐户**，请选择 "**上传 Blob** " 图标。

        ![新项目的 HDInsight 上传图标](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/hdinsight-upload-icon.png)

        在 "**上载新文件**" 对话框中的 "**文件名**" 下，选择 "**浏览**"。 在 "**上传 Blob** " 对话框中，打开*hivecsharp.exe*项目的*bin\debug*文件夹，然后选择 " *hivecsharp.exe* " 文件。 最后，选择 "**打开**"，然后选择 **"确定"** 完成上传。

    * 如果使用**Azure Data Lake Storage**，请右键单击文件列表中的空白区域，然后选择 "**上传**"。 最后，选择*hivecsharp.exe*文件，并选择 "**打开**"。

    上传“HiveCSharp.exe”完成后，请为“PigUDF.exe”文件重复该上传过程。

## <a name="run-an-apache-hive-query"></a>运行 Apache Hive 查询

现在，你可以运行使用 Hive UDF 应用程序的 Hive 查询。

1. 在 Visual Studio 中，导航到 "**查看** > "**服务器资源管理器**"。

2. 依次展开“Azure”和“HDInsight”。

3. 右键单击已将 *HiveCSharp* 应用程序部署到的群集，然后选择“编写 Hive 查询”。

4. 请使用以下文本执行 Hive 查询：

    ```hiveql
    -- Uncomment the following if you are using Azure Storage
    -- add file wasbs:///HiveCSharp.exe;
    -- Uncomment the following if you are using Azure Data Lake Storage Gen1
    -- add file adl:///HiveCSharp.exe;
    -- Uncomment the following if you are using Azure Data Lake Storage Gen2
    -- add file abfs:///HiveCSharp.exe;

    SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'HiveCSharp.exe' AS
    (clientid string, phoneLabel string, phoneHash string)
    FROM hivesampletable
    ORDER BY clientid LIMIT 50;
    ```

    > [!IMPORTANT]
    > 取消注释与用于群集的默认存储类型相匹配的 `add file` 语句。

    此查询从 `hivesampletable`中选择 `clientid`、`devicemake`和 `devicemodel` 字段，然后将这些字段传递到*hivecsharp.exe*应用程序。 该查询预期应用程序返回三个字段，它们将存储为 `clientid`、`phoneLabel` 和 `phoneHash`。 查询还需要在默认存储容器的根目录中找到*hivecsharp.exe* 。

5. 将默认**交互**切换为 "**批处理**"，然后选择 "**提交**" 将作业提交到 HDInsight 群集。 此时会打开“Hive 作业摘要”窗口。

6. 选择 "**刷新**" 以刷新摘要，直到 "**作业状态**" 更改为 "**已完成**"。 若要查看作业输出，请选择 "**作业输出**"。

## <a name="run-an-apache-pig-job"></a>运行 Apache Pig 作业

还可以运行使用 Pig UDF 应用程序的 Pig 作业。

1. 使用 SSH 连接到 HDInsight 群集。 （例如，运行命令 `ssh sshuser@<clustername>-ssh.azurehdinsight.net`。）有关详细信息，请参阅[使用 SSH withHDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md)。

2. 使用以下命令启动 Pig 命令行：

    ```shell
    pig
    ```

    此时会显示 `grunt>` 提示。

3. 输入以下命令以运行使用 .NET Framework 应用程序的 Pig 作业：

    ```pig
    DEFINE streamer `PigUDF.exe` CACHE('/PigUDF.exe');
    LOGS = LOAD '/example/data/sample.log' as (LINE:chararray);
    LOG = FILTER LOGS by LINE is not null;
    DETAILS = STREAM LOG through streamer as (col1, col2, col3, col4, col5);
    DUMP DETAILS;
    ```

    `DEFINE` 语句将为*pigudf.exe*应用程序创建 `streamer` 的别名，并 `CACHE` 从群集的默认存储中加载它。 稍后，`streamer` 与 `STREAM` 运算符一起使用来处理 `LOG` 中包含的单个行，并将数据作为一系列列返回。

    > [!NOTE]
    > 用于流式处理的应用程序名称必须在带有别名的情况下由 \` （反撇号）字符括起来，当与 `SHIP`一起使用时，必须由 ' （单引号）字符括起来。

4. 在输入最后一行后，该作业应该启动。 它将返回类似于以下文本的输出：

    ```output
    (2019-07-15 16:43:25 SampleClass5 [WARN] problem finding id 1358451042 - java.lang.Exception)
    (2019-07-15 16:43:25 SampleClass5 [DEBUG] detail for id 1976092771)
    (2019-07-15 16:43:25 SampleClass5 [TRACE] verbose detail for id 1317358561)
    (2019-07-15 16:43:25 SampleClass5 [TRACE] verbose detail for id 1737534798)
    (2019-07-15 16:43:25 SampleClass7 [DEBUG] detail for id 1475865947)
    ```

5. 使用 `exit` 退出 pig。

## <a name="next-steps"></a>后续步骤

本文档介绍了如何在 HDInsight 上使用 Hive 和 Pig 中的 .NET Framework 应用程序。 如果希望了解如何将 Python 与 Hive 和 Pig 配合使用，请参阅[在 HDInsight 中将 Python 与 Apache Hive 和 Apache Pig 配合使用](python-udf-hdinsight.md)。

有关使用 Hive 的其他方式以及如何使用 MapReduce，请参阅以下文章：

* [将 Apache Hive 和 HDInsight 配合使用](hdinsight-use-hive.md)
* [将 MapReduce 与 HDInsight 配合使用](hdinsight-use-mapreduce.md)
* [Pig 拉丁语基础知识](https://pig.apache.org/docs/latest/basic.html)