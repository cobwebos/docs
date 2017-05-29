---
title: "使用 HDInsight 开发 Python MapReduce 作业 | Microsoft Docs"
description: "了解如何在基于 Linux 的 HDInsight 群集上创建和运行 Python MapReduce 作业。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7631d8d9-98ae-42ec-b9ec-ee3cf7e57fb3
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/03/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: 7c4d5e161c9f7af33609be53e7b82f156bb0e33f
ms.openlocfilehash: a33a2b64bba917f186437f071cb0ee2326154879
ms.contentlocale: zh-cn
ms.lasthandoff: 05/04/2017


---
# <a name="develop-python-streaming-programs-for-hdinsight"></a>开发适用于 HDInsight 的 Python 流式处理程序

了解如何在 MapReduce 操作中使用 Python。 Hadoop 为 MapReduce 提供了一个流式处理 API，使你能够以 Java 之外的其他语言来编写映射和化简函数。 本文档中的步骤实现 Python 中的映射和化简组件。

## <a name="prerequisites"></a>先决条件

* 基于 Linux 的 HDInsight 上的 Hadoop 群集

  > [!IMPORTANT]
  > 本文档中的步骤需要使用 Linux 的 HDInsight 群集。 Linux 是 HDInsight 3.4 或更高版本上使用的唯一操作系统。 有关详细信息，请参阅 [HDInsight 组件版本控制](hdinsight-component-versioning.md#hdi-version-33-nearing-deprecation-date)。

* 文本编辑器

  > [!IMPORTANT]
  > 文本编辑器必须使用 LF 作为行尾。 在基于 Linux 的 HDInsight 群集上运行 MapReduce 作业时，使用 CRLF 的行尾会导致出错。

* `ssh` 和 `scp` 命令，或 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-3.8.0)

## <a name="word-count"></a>字数统计

本示例是使用 python 中的映射器和化简器实现的基本字数统计。 映射器会将句子分解成不同的单词，而化简器会汇总单词并统计字数以生成输出。

下图演示了在映射和化简阶段发生的情况。

![mapreduce 进程的说明](./media/hdinsight-hadoop-streaming-python/HDI.WordCountDiagram.png)

## <a name="streaming-mapreduce"></a>流式处理 MapReduce

Hadoop 允许你指定包含作业所用映射和化简逻辑的文件。 映射和化简逻辑的具体要求如下：

* **输入**：映射和化简组件必须从 STDIN 读取输入数据。
* **输出**：映射和化简组件必须将输出数据写入到 STDOUT。
* **数据格式**：使用和生成的数据必须是键/值对，并以制表符分隔。

Python 可以使用 `sys` 模块从 STDIN 读取数据，并使用 `print` 输出到 STDOUT，从而可以轻松应对这些要求。 余下的任务就是在键和值之间以制表符 (`\t`) 设置数据的格式。

## <a name="create-the-mapper-and-reducer"></a>创建映射器和化简器

1. 创建名为 `mapper.py` 的文件并使用以下代码作为内容：

   ```python
   #!/usr/bin/env python

   # Use the sys module
   import sys

   # 'file' in this case is STDIN
   def read_input(file):
       # Split each line into words
       for line in file:
           yield line.split()

   def main(separator='\t'):
       # Read the data using read_input
       data = read_input(sys.stdin)
       # Process each words returned from read_input
       for words in data:
           # Process each word
           for word in words:
               # Write to STDOUT
               print '%s%s%d' % (word, separator, 1)

   if __name__ == "__main__":
       main()
   ```

2. 创建名为“reducer.py”的文件并使用以下代码作为内容：

   ```python
   #!/usr/bin/env python

   # import modules
   from itertools import groupby
   from operator import itemgetter
   import sys

   # 'file' in this case is STDIN
   def read_mapper_output(file, separator='\t'):
       # Go through each line
       for line in file:
           # Strip out the separator character
           yield line.rstrip().split(separator, 1)

   def main(separator='\t'):
       # Read the data using read_mapper_output
       data = read_mapper_output(sys.stdin, separator=separator)
       # Group words and counts into 'group'
       #   Since MapReduce is a distributed process, each word
       #   may have multiple counts. 'group' will have all counts
       #   which can be retrieved using the word as the key.
       for current_word, group in groupby(data, itemgetter(0)):
           try:
               # For each word, pull the count(s) for the word
               #   from 'group' and create a total count
               total_count = sum(int(count) for current_word, count in group)
               # Write to stdout
               print "%s%s%d" % (current_word, separator, total_count)
           except ValueError:
               # Count was not a number, so do nothing
               pass

   if __name__ == "__main__":
       main()
   ```

## <a name="run-using-powershell"></a>使用 PowerShell 运行

若要确保文件具有适当的行尾，请使用以下 PowerShell 脚本：

```powershell
# Set $original_file to the Python file name
$text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
[IO.File]::WriteAllText($original_file, $text)
```

使用以下 PowerShell 脚本上传文件、运行作业以及查看输出：

```powershell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Add-AzureRmAccount
}

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
# Get the login (HTTPS) credentials for the cluster
$creds=Get-Credential -Message "Enter the login for the cluster" -UserName "admin"
$clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
$storageInfo = $clusterInfo.DefaultStorageAccount.split('.')
$defaultStoreageType = $storageInfo[1]
$defaultStorageName = $storageInfo[0]

# Progress indicator
$activity="Python MapReduce"
Write-Progress -Activity $activity -Status "Uploading mapper and reducer..."

# Upload the files
switch ($defaultStoreageType)
{
    "blob" {
        # Get the blob storage information for the cluster
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageContainer=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $defaultStorageName `
            -ResourceGroupName $resourceGroup)[0].Value
        # Create a storage context and upload the file
        $context = New-AzureStorageContext `
            -StorageAccountName $defaultStorageName `
            -StorageAccountKey $storageAccountKey
        # Upload the mapper.py file
        Set-AzureStorageBlobContent `
            -File .\mapper.py `
            -Blob "mapper.py" `
            -Container $storageContainer `
            -Context $context
        # Upload the reducer.py file
        Set-AzureStorageBlobContent `
            -File .\reducer.py `
            -Blob "reducer.py" `
            -Container $storageContainer `
            -Context $context `
    }
    "azuredatalakestore" {
        # Get the Data Lake Store name
        # Get the root of the HDInsight cluster azuredatalakestore
        $clusterRoot=$clusterInfo.DefaultStorageRootPath
        # Upload the files. Prepend the destination with the cluster root
        Import-AzureRmDataLakeStoreItem -AccountName $defaultStorageName `
            -Path .\mapper.py `
            -Destination "$clusterRoot/mapper.py" `
            -Force
        Import-AzureRmDataLakeStoreItem -AccountName $defaultStorageName `
            -Path .\reducer.py `
            -Destination "$clusterRoot/reducer.py" `
            -Force
    }
    default {
        Throw "Unknown storage type: $defaultStoreageType"
    }
}

# Create the streaming job definition
# Note: This assumes that the mapper.py and reducer.py
#       are in the root of default storage. If you put them in a
#       subdirectory, change the -Files parameter to the correct path.
$jobDefinition = New-AzureRmHDInsightStreamingMapReduceJobDefinition `
    -Files "/mapper.py", "/reducer.py" `
    -Mapper "mapper.py" `
    -Reducer "reducer.py" `
    -InputPath "/example/data/gutenberg/davinci.txt" `
    -OutputPath "/example/wordcountout"

# Start the job
Write-Progress -Activity $activity -Status "Starting the MapReduce job..."
$job = Start-AzureRmHDInsightJob `
    -ClusterName $clusterName `
    -JobDefinition $jobDefinition `
    -HttpCredential $creds

# Wait for the job to complete
Write-Progress -Activity $activity -Status "Waiting for the job to complete..."
Wait-AzureRmHDInsightJob `
    -JobId $job.JobId `
    -ClusterName $clusterName `
    -HttpCredential $creds

# Display the results of the job
Write-Progress -Activity $activity -Status "Downloading job output..."
switch ($defaultStoreageType)
{
    "blob" {
        # Get the blob storage information for the cluster
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageContainer=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $defaultStorageName `
            -ResourceGroupName $resourceGroup)[0].Value
        # Create a storage context and download the file
        $context = New-AzureStorageContext `
            -StorageAccountName $defaultStorageName `
            -StorageAccountKey $storageAccountKey
        # Download the file
        Get-AzureStorageBlobContent `
            -Container $storageContainer `
            -Blob "example/wordcountout/part-00000" `
            -Context $context `
            -Destination "./output.txt"
        # Display the output
        Get-Content "./output.txt"
    }
    "azuredatalakestore" {
        # Get the Data Lake Store name
        # Get the root of the HDInsight cluster azuredatalakestore
        $clusterRoot=$clusterInfo.DefaultStorageRootPath
        # Download the file. Prepend the destination with the cluster root
        # NOTE: Unlike getting a blob, this just gets the content and no
        #       file is created locally.
        $sourcePath=$clusterRoot + "example/wordcountout/part-00000"
        Get-AzureRmDataLakeStoreItemContent -Account $defaultStorageName -Path $sourcePath -Confirm
    }
    default {
        Throw "Unknown storage type: $defaultStoreageType"
    }
}
```

## <a name="run-from-an-ssh-session"></a>从 SSH 会话运行

1. 在开发环境中，在与 `mapper.py` 和 `reducer.py` 文件相同的目录中，使用以下命令：

    ```bash
    scp mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:
    ```

    将 `username` 替换为群集的 SSH 用户名，并将 `clustername` 替换为群集的名称。

    此命令会将两个文件从本地系统复制到头节点。

    > [!NOTE]
    > 如果你使用了密码来保护 SSH 帐户，系统会提示你输入密码。 如果你使用了 SSH 密钥，则可能需要使用 `-i` 参数和私钥路径，例如 `scp -i /path/to/private/key mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:`。

2. 通过使用 SSH 连接到群集：

    ```bash
    ssh username@clustername-ssh.azurehdinsight.net`
    ```

    有关详细信息，请参阅 [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)（对 HDInsight 使用 SSH）。

3. 若要确保 mapper.py 和 reducer.py 具有正确的行尾，请使用以下命令：

    ```bash
    perl -pi -e 's/\r\n/\n/g' mappery.py
    perl -pi -e 's/\r\n/\n/g' reducer.py
    ```

4. 使用以下命令启动 MapReduce 作业。

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files mapper.py,reducer.py -mapper mapper.py -reducer reducer.py -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
    ```

    此命令包括以下几个部分：

   * **hadoop-streaming.jar**：运行流式处理 MapReduce 操作时使用。 它可以将 Hadoop 和你提供的外部 MapReduce 代码连接起来。

   * **-files**：将指定的文件添加到 MapReduce 作业。

   * **-mapper**：告知 Hadoop 要用作映射器的文件。

   * **-reducer**：告知 Hadoop 要用作化简器的文件。

   * **-input**：要从中统计字数的输入文件。

   * **-output**：输出将写入到的目录。

    当 MapReduce 作业运行时，将以百分比形式显示进程。

        15/02/05 19:01:04 INFO mapreduce.Job:  映射 0% 化简 0%    15/02/05 19:01:16 INFO mapreduce.Job:  映射 100% 化简 0%    15/02/05 19:01:27 INFO mapreduce.Job:  映射 100% 化简 100%


5. 若要查看输出，请使用以下命令：

    ```bash
    hdfs dfs -text /example/wordcountout/part-00000
    ```

    此命令会显示单词和单词出现次数的列表。

## <a name="next-steps"></a>后续步骤

既然你了解了如何将流式处理 MapRedcue 作业用于 HDInsight，就使用以下链接来学习 Azure HDInsight 的其他用法。

* [将 Hive 与 HDInsight 配合使用](hdinsight-use-hive.md)
* [将 Pig 与 HDInsight 配合使用](hdinsight-use-pig.md)
* [将 MapReduce 作业与 HDInsight 配合使用](hdinsight-use-mapreduce.md)

