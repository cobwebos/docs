---
title: "在 HDInsight 中将 Hadoop Pig 与 .NET 配合使用 | Microsoft Docs"
description: "了解如何使用 .NET SDK for Hadoop 将 Pig 作业提交到 HDInsight 上的 Hadoop。"
services: hdinsight
documentationcenter: .net
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: fa11d49a-328c-47e7-b16d-e7ed2a453195
ms.service: hdinsight
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/08/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: e80bf82df28fbce8a1019c6eb07cfcae4cbba930
ms.openlocfilehash: e32d21ca410d4107f68f8b72353ca400a26c4523


---
# <a name="run-pig-jobs-using-the-net-sdk-for-hadoop-in-hdinsight"></a>使用 HDInsight 中的 .NET SDK for Hadoop 运行 Pig 作业
[!INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

本文档提供使用 .NET SDK for Hadoop 将 Pig 作业提交到 HDInsight 上的 Hadoop 群集的示例。

HDInsight .NET SDK 提供 .NET 客户端库，可简化从 .NET 中使用 HDInsight 群集的操作。 Pig 可让你通过为一系列数据转换建模，来创建 MapReduce 操作。 本文档介绍如何使用基本 C# 应用程序将 Pig 作业提交到 HDInsight 群集。

## <a name="prerequisites"></a>先决条件

若要完成本文中的步骤，需要以下各项。

* Azure HDInsight (Hadoop on HDInsight) 群集（基于 Windows 或 Linux）。

  > [!IMPORTANT]
  > Linux 是 HDInsight 3.4 或更高版本上使用的唯一操作系统。 有关详细信息，请参阅 [HDInsight Deprecation on Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)（HDInsight 在 Windows 上即将弃用）。

* Visual Studio 2012、2013 或 2015

## <a name="create-the-application"></a>创建应用程序

HDInsight .NET SDK 提供 .NET 客户端库，可简化从 .NET 中使用 HDInsight 群集的操作。 

1. 在 Visual Studio 的“文件”菜单中，选择“新建”，然后选择“项目”。

2. 对于新项目，请键入或选择以下值。
   
    <table>
    <tr>
    <th>属性</th>
    <th>值</th>
    </tr>
    <tr>
    <th>类别</th>
    <th>模板/Visual C#/Windows</th>
    </tr>
    <tr>
    <th>模板</th>
    <th>控制台应用程序</th>
    </tr>
    <tr>
    <th>Name</th>
    <th>SubmitPigJob</th>
    </tr>
    </table>

3.  。

4. 从“工具”菜单中选择“库包管理器”或“Nuget 包管理器”，然后选择“包管理器控制台”。

5. 在控制台中运行以下命令，以安装 .NET SDK 包。
   
        Install-Package Microsoft.Azure.Management.HDInsight.Job

6. 在“解决方案资源管理器”中，双击“Program.cs”将其打开。 将现有代码替换为以下内容。
   
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
            private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
            private const string ExistingClusterUsername = "<Cluster Username>";
            private const string ExistingClusterPassword = "<Cluster User Password>";

            static void Main(string[] args)
            {
                System.Console.WriteLine("The application is running ...");

                var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);

                SubmitPigJob();

                System.Console.WriteLine("Press ENTER to continue ...");
                System.Console.ReadLine();
            }

            private static void SubmitPigJob()
            {
                var parameters = new PigJobSubmissionParameters
                {
                    Query = @"LOGS = LOAD '/example/data/sample.log';
                                LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
                                FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
                                GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
                                FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
                                RESULT = order FREQUENCIES by COUNT desc;
                                DUMP RESULT;"
                };

                System.Console.WriteLine("Submitting the Pig job to the cluster...");
                var response = _hdiJobManagementClient.JobManagement.SubmitPigJob(parameters);
                System.Console.WriteLine("Validating that the response is as expected...");
                System.Console.WriteLine("Response status code is " + response.StatusCode);
                System.Console.WriteLine("Validating the response object...");
                System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
            }
        }
    }
    ```
    
7. 按 **F5** 启动应用程序。

8. 按 **Enter** 退出应用程序。

## <a name="summary"></a>摘要

如你所见，.NET SDK for Hadoop 可让你创建 .NET 应用程序，从而将 Pig 作业提交到 HDInsight 群集和监视作业状态。

## <a name="next-steps"></a>后续步骤

有关 HDInsight 中的 Pig 的一般信息。

* [将 Pig 与 Hadoop on HDInsight 配合使用](hdinsight-use-pig.md)

有关 HDInsight 上的 Hadoop 的其他使用方法的信息。

* [将 Hive 与 Hadoop on HDInsight 配合使用](hdinsight-use-hive.md)
* [将 MapReduce 与 HDInsight 上的 Hadoop 配合使用](hdinsight-use-mapreduce.md)

[preview-portal]: https://portal.azure.com/



<!--HONumber=Feb17_HO2-->


