---
title: 使用 .NET 和 HDInsight 运行 Apache Sqoop 作业 - Azure
description: 了解如何使用 HDInsight .NET SDK 在 Apache Hadoop 群集和 Azure SQL 数据库之间运行 Apache Sqoop 导入和导出。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 01/14/2020
ms.openlocfilehash: f0f767273a40bc91b1d49477c896b0b157623106
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2020
ms.locfileid: "76157054"
---
# <a name="run-apache-sqoop-jobs-by-using-net-sdk-for-apache-hadoop-in-hdinsight"></a>使用 HDInsight 中用于 Apache Hadoop 的 .NET SDK 运行 Apache Sqoop 作业

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

了解如何使用 Azure HDInsight .NET SDK 在 HDInsight 中运行 Apache Sqoop 作业，以在 HDInsight 群集和 Azure SQL 数据库或 SQL Server 数据库之间进行导入和导出。

## <a name="prerequisites"></a>必备组件

* 从将[Apache Sqoop 与 HDInsight 中的 Hadoop 配合使用](./hdinsight-use-sqoop.md)来完成[设置测试环境](./hdinsight-use-sqoop.md#create-cluster-and-sql-database)。

* [Visual Studio](https://visualstudio.microsoft.com/vs/community/)。

* 熟悉 Sqoop。 有关详细信息，请参阅[Sqoop 用户指南](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html)。

## <a name="use-sqoop-on-hdinsight-clusters-with-the-net-sdk"></a>通过 .NET SDK 使用 HDInsight 群集上的 Sqoop

HDInsight .NET SDK 提供 .NET 客户端库，以便可轻易从 .NET 中使用 HDInsight 群集。 在本部分中，将创建C#一个控制台应用程序，用于将 `hivesampletable` 导出到从先决条件创建的 Azure SQL 数据库表。

## <a name="set-up"></a>设置

1. 启动 Visual Studio 并创建一个C#控制台应用程序。

1. 导航到 "**工具**" > **NuGet 包管理器**" > **程序包管理器控制台**"，并运行以下命令：

    ```
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```

## <a name="sqoop-export"></a>Sqoop 导出

从 Hive 到 SQL Server。  此示例将来自 Hive `hivesampletable` 表的数据导出到 SQL 数据库中的 `mobiledata` 表。

1. 在 Program.cs 文件中使用以下代码。 编辑代码以设置 `ExistingClusterName`的值和 `ExistingClusterPassword`。

    ```csharp
    using Microsoft.Azure.Management.HDInsight.Job;
    using Microsoft.Azure.Management.HDInsight.Job.Models;
    using Hyak.Common;
    
    namespace SubmitHDInsightJobDotNet
    {
        class Program
        {
            private static HDInsightJobManagementClient _hdiJobManagementClient;
    
            private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
            private const string ExistingClusterPassword = "<Cluster User Password>";
            private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
            private const string ExistingClusterUsername = "admin";
    
            static void Main(string[] args)
            {
                System.Console.WriteLine("The application is running ...");
    
                var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
    
                SubmitSqoopJob();
    
                System.Console.WriteLine("Press ENTER to continue ...");
                System.Console.ReadLine();
            }
    
            private static void SubmitSqoopJob()
            {
                var sqlDatabaseServerName = ExistingClusterName + "dbserver";
                var sqlDatabaseLogin = "sqluser";
                var sqlDatabaseLoginPassword = ExistingClusterPassword;
                var sqlDatabaseDatabaseName = ExistingClusterName + "db";
    
                // Connection string for using Azure SQL Database; Comment if using SQL Server
                var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + "@" + sqlDatabaseServerName + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
    
                // Connection string for using SQL Server; Uncomment if using SQL Server
                // var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ";user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
    
                //sqoop start
                var tableName = "mobiledata";
    
                var parameters = new SqoopJobSubmissionParameters
                {
                     Command = "export --connect " + connectionString + " --table " + tableName + " --hcatalog-table hivesampletable"
                };
                //sqoop end
    
                System.Console.WriteLine("Submitting the Sqoop job to the cluster...");
                var response = _hdiJobManagementClient.JobManagement.SubmitSqoopJob(parameters);
                System.Console.WriteLine("Validating that the response is as expected...");
                System.Console.WriteLine("Response status code is " + response.StatusCode);
                System.Console.WriteLine("Validating the response object...");
                System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
            }
        }
    }
    ```

1. 若要运行程序，请选择 F5 键。

## <a name="sqoop-import"></a>Sqoop 导入

从 SQL Server 到 Azure 存储。 此示例依赖于已执行的上述导出。  此示例将数据从 SQL 数据库中的 `mobiledata` 表导入到群集的默认存储帐户上的 `wasb:///tutorials/usesqoop/importeddata` 目录。

1. 将 `//sqoop start //sqoop end` 块中上面的代码替换为以下代码：

    ```csharp
    var tableName = "mobiledata";
    var exportDir = "/tutorials/usesqoop/importeddata";
    
    var parameters = new SqoopJobSubmissionParameters
    {
        Command = "import --connect " + connectionString + " --table " + tableName + " --target-dir " +  exportDir + " --fields-terminated-by \\t --lines-terminated-by \\n -m 1"
    };
    ```

1. 若要运行程序，请选择 F5 键。

## <a name="limitations"></a>限制

基于 Linux 的 HDInsight 存在以下限制：

* 大容量导出：用于将数据导出到 Microsoft SQL Server 或 Azure SQL 数据库的 Sqoop 连接器目前不支持批量插入。

* 批处理：通过使用 `-batch` 开关，Sqoop 将执行多个插入而不是批处理插入操作。

## <a name="next-steps"></a>后续步骤

现在，你已学习了如何使用 Sqoop。 若要了解更多信息，请参阅以下文章：

* [将 Apache Oozie 与 HDInsight 配合使用](../hdinsight-use-oozie-linux-mac.md)：在 Oozie 工作流中使用 Sqoop 操作。
* [将数据上传到 HDInsight](../hdinsight-upload-data.md)：了解将数据上传到 HDInsight 或 Azure Blob 存储的其他方法。
