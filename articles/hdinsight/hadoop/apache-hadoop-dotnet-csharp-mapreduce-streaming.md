---
title: 在 HDInsight 中的 Hadoop 上将 C# 与 MapReduce 配合使用 - Azure | Microsoft Docs
description: 了解如何在 Azure HDInsight 中通过 Hadoop 使用 C# 创建 MapReduce 解决方案。
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: d83def76-12ad-4538-bb8e-3ba3542b7211
ms.custom: hdinsightactive
ms.service: hdinsight
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: larryfr
ms.openlocfilehash: 7287972ccf63f33a8cf08065f8d5d30ee1b1afb5
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
ms.locfileid: "31400871"
---
# <a name="use-c-with-mapreduce-streaming-on-hadoop-in-hdinsight"></a>在 HDInsight 中的 Hadoop 上将 C# 与 MapReduce 流式处理配合使用

了解如何在 HDInsight 上使用 C# 创建 MapReduce 解决方案。

> [!IMPORTANT]
> Linux 是 HDInsight 3.4 或更高版本上使用的唯一操作系统。 有关详细信息，请参阅 [HDInsight 组件版本控制](../hdinsight-component-versioning.md)。

Hadoop 流式处理是一个实用工具，通过它可以使用脚本或可执行文件运行 MapReduce 作业。 在本示例中，.NET 用于为单词计数解决方案实现映射器和化简器。

## <a name="net-on-hdinsight"></a>HDInsight 上的 .NET

__基于 Linux 的 HDInsight__ 群集使用 [Mono (https://mono-project.com)](https://mono-project.com) 运行 .NET 应用程序。 HDInsight 版本 3.6 附带了 Mono 版本 4.2.1。 有关包含在 HDInsight 中的 Mono 版本的详细信息，请参阅 [HDInsight 组件版本](../hdinsight-component-versioning.md)。 若要使用 Mono 的特定版本，请参阅[安装或更新 Mono](../hdinsight-hadoop-install-mono.md) 文档。

有关 Mono 与 .NET Framework 版本的兼容性的详细信息，请参阅 [Mono 兼容性](http://www.mono-project.com/docs/about-mono/compatibility/)。

## <a name="how-hadoop-streaming-works"></a>Hadoop 流式处理的工作原理

在本文档中用于流式处理的基本流程如下所示：

1. Hadoop 在 STDIN 上将数据传递到映射器（在本示例中为 mapper.exe）。
2. 映射器处理数据，并向 STDOUT 发出制表符分隔的键/值对。
3. 该输出由 Hadoop 读取，随后将传递到 STDIN 上的化简器（在本示例中为 reducer.exe）。
4. 化简器将读取制表符分隔的键/值对、处理数据，然后将结果作为制表符分隔的键/值对在 STDOUT 上发出。
5. 该输出由 Hadoop 读取，并写入输出目录。

有关流式处理的详细信息，请参阅 [Hadoop 流式处理 (https://hadoop.apache.org/docs/r2.7.1/hadoop-streaming/HadoopStreaming.html)](https://hadoop.apache.org/docs/r2.7.1/hadoop-streaming/HadoopStreaming.html)。

## <a name="prerequisites"></a>先决条件

* 熟悉编写和生成面向 .NET Framework 4.5 的 C# 代码。 本文档中的各个步骤都使用 Visual Studio 2017。

* 将 .exe 文件上传到群集的方法。 本文档中的各个步骤都使用针对 Visual Studio 的 Data Lake 工具将文件上传到群集的主要存储。

* Azure PowerShell 或 SSH 客户端。

* HDInsight 群集上的 Hadoop。 有关创建群集的详细信息，请参阅[创建 HDInsight 群集](../hdinsight-hadoop-provision-linux-clusters.md)。

## <a name="create-the-mapper"></a>创建映射器

在 Visual Studio 中，创建名为 __mapper__ 的新__控制台应用程序__。 针对该应用程序使用以下代码：

```csharp
using System;
using System.Text.RegularExpressions;

namespace mapper
{
    class Program
    {
        static void Main(string[] args)
        {
            string line;
            //Hadoop passes data to the mapper on STDIN
            while((line = Console.ReadLine()) != null)
            {
                // We only want words, so strip out punctuation, numbers, etc.
                var onlyText = Regex.Replace(line, @"\.|;|:|,|[0-9]|'", "");
                // Split at whitespace.
                var words = Regex.Matches(onlyText, @"[\w]+");
                // Loop over the words
                foreach(var word in words)
                {
                    //Emit tab-delimited key/value pairs.
                    //In this case, a word and a count of 1.
                    Console.WriteLine("{0}\t1",word);
                }
            }
        }
    }
}
```

创建该应用程序后，生成它以在项目目录中生成 `/bin/Debug/mapper.exe` 文件。

## <a name="create-the-reducer"></a>创建化简器

在 Visual Studio 中，创建名为 __reducer__ 的新__控制台应用程序__。 针对该应用程序使用以下代码：

```csharp
using System;
using System.Collections.Generic;

namespace reducer
{
    class Program
    {
        static void Main(string[] args)
        {
            //Dictionary for holding a count of words
            Dictionary<string, int> words = new Dictionary<string, int>();

            string line;
            //Read from STDIN
            while ((line = Console.ReadLine()) != null)
            {
                // Data from Hadoop is tab-delimited key/value pairs
                var sArr = line.Split('\t');
                // Get the word
                string word = sArr[0];
                // Get the count
                int count = Convert.ToInt32(sArr[1]);

                //Do we already have a count for the word?
                if(words.ContainsKey(word))
                {
                    //If so, increment the count
                    words[word] += count;
                } else
                {
                    //Add the key to the collection
                    words.Add(word, count);
                }
            }
            //Finally, emit each word and count
            foreach (var word in words)
            {
                //Emit tab-delimited key/value pairs.
                //In this case, a word and a count of 1.
                Console.WriteLine("{0}\t{1}", word.Key, word.Value);
            }
        }
    }
}
```

创建该应用程序后，生成它以在项目目录中生成 `/bin/Debug/reducer.exe` 文件。

## <a name="upload-to-storage"></a>上传到存储

1. 在 Visual Studio 中，打开“服务器资源管理器”。

2. 依次展开“Azure”和“HDInsight”。

3. 如果出现提示，请输入 Azure 订阅凭据，并单击“登录”。

4. 展开要将此应用程序部署到的 HDInsight 群集。 将列出带有文本“（默认存储帐户）”的条目。

    ![显示群集存储帐户的服务器资源管理器](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/storage.png)

    * 如果此条目可以展开，则在使用 __Azure 存储帐户__作为该群集的默认存储。 要查看该群集的默认存储上的文件，请展开该条目，并双击“（默认容器）”。

    * 如果此条目无法展开，则在使用 __Azure Data Lake Store__ 作为该群集的默认存储。 若要查看该群集的默认存储上的文件，请双击“（默认存储帐户）”条目。

5. 若要上传 .exe 文件，请使用以下方法之一：

    * 如果使用的是 __Azure 存储帐户__，请单击“上传”图标，并浏览到“mapper” 项目的“bin\debug”文件夹。 最后，选择“mapper.exe”文件，并单击“确定”。

        ![上传图标](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/upload.png)
    
    * 如果使用的是 __Azure Data Lake Store__，请右键单击文件列表中的空白区域，并选择“上传”。 最后，选择“mapper.exe”文件，并单击“打开”。

    上传“mapper.exe”完成后，请为“reducer.exe”文件重复该上传过程。

## <a name="run-a-job-using-an-ssh-session"></a>运行作业：使用 SSH 会话

1. 使用 SSH 连接到 HDInsight 群集。 有关详细信息，请参阅 [Use SSH with HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md)（对 HDInsight 使用 SSH）。

2. 使用以下命令之一启动 MapReduce 作业：

    * 如果使用 __Data Lake Store__ 作为默认存储：

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files adl:///mapper.exe,adl:///reducer.exe -mapper mapper.exe -reducer reducer.exe -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
        ```
    
    * 如果使用 __Azure 存储__作为默认存储：

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files wasb:///mapper.exe,wasb:///reducer.exe -mapper mapper.exe -reducer reducer.exe -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
        ```

    下表描述每个参数的作用：

    * `hadoop-streaming.jar`：包含流式处理 MapReduce 功能的 jar 文件。
    * `-files`：将 `mapper.exe` 和 `reducer.exe` 文件添加到此作业。 每个文件前的 `adl:///` 或 `wasb:///` 是指向群集的默认存储根目录的路径。
    * `-mapper`：指定哪个文件实现映射器。
    * `-reducer`：指定哪个文件实现化简器。
    * `-input`：输入数据。
    * `-output`：输出目录。

3. 完成 MapReduce 作业后，使用以下命令查看结果：

    ```bash
    hdfs dfs -text /example/wordcountout/part-00000
    ```

    以下文本是此命令返回的数据的示例：

        you     1128
        young   38
        younger 1
        youngest        1
        your    338
        yours   4
        yourself        34
        yourselves      3
        youth   17

## <a name="run-a-job-using-powershell"></a>运行作业：使用 PowerShell

使用以下 PowerShell 脚本运行 MapReduce 作业，并下载结果。

[!code-powershell[main](../../../powershell_scripts/hdinsight/use-csharp-mapreduce/use-csharp-mapreduce.ps1?range=5-87)]

此脚本会提示用户提供群集登录的帐户名和密码，以及 HDInsight 群集名称。 作业完成后，输出会下载到名为 `output.txt` 的文件中。 以下文本是 `output.txt` 文件中数据的示例：

    you     1128
    young   38
    younger 1
    youngest        1
    your    338
    yours   4
    yourself        34
    yourselves      3
    youth   17

## <a name="next-steps"></a>后续步骤

有关将 MapReduce 与 HDInsight 配合使用的详细信息，请参阅[将 MapReduce 与 HDInsight 配合使用](hdinsight-use-mapreduce.md)。

有关将 C# 与 Hive 和 Pig 配合使用的信息，请参阅[将 C# 用户定义函数与 Hive 和 Pig 配合使用](apache-hadoop-hive-pig-udf-dotnet-csharp.md)。

有关在 HDInsight 上将 C# 与 Storm 配合使用的信息，请参阅[为 HDInsight 上的 Storm 开发 C# 拓扑](../storm/apache-storm-develop-csharp-visual-studio-topology.md)。
