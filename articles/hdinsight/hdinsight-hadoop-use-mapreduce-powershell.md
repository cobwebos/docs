---
title: "将 MapReduce 和 PowerShell 与 Hadoop 配合使用 — Azure HDInsight | Microsoft Docs"
description: "了解如何使用 PowerShell 在 HDInsight 上的 Hadoop 上远程运行 MapReduce 作业。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 21b56d32-1785-4d44-8ae8-94467c12cfba
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/06/2017
ms.author: larryfr
ms.translationtype: HT
ms.sourcegitcommit: eeed445631885093a8e1799a8a5e1bcc69214fe6
ms.openlocfilehash: bc4e1b04e8f90e5713468ce38f6f05174affb261
ms.contentlocale: zh-cn
ms.lasthandoff: 09/07/2017

---
# <a name="run-mapreduce-jobs-with-hadoop-on-hdinsight-using-powershell"></a>使用 PowerShell 在 HDInsight 上的 Hadoop 上运行 MapReduce 作业

[!INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

本文档提供使用 Azure PowerShell 在 HDInsight 上的 Hadoop 群集中运行 MapReduce 作业的示例。

## <a id="prereq"></a>先决条件

* **Azure HDInsight（HDInsight 上的 Hadoop）群集**

  > [!IMPORTANT]
  > Linux 是 HDInsight 3.4 或更高版本上使用的唯一操作系统。 有关详细信息，请参阅 [HDInsight 在 Windows 上停用](hdinsight-component-versioning.md#hdinsight-windows-retirement)。

* **配备 Azure PowerShell 的工作站**。

## <a id="powershell"></a>使用 Azure PowerShell 运行 MapReduce 作业

Azure PowerShell 提供 *cmdlet*，可在 HDInsight 上远程运行 MapReduce 作业。 从内部来讲，PowerShell 将对 HDInsight 群集上运行的 [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat)（以前称为 Templeton）进行 REST 调用。

在远程 HDInsight 群集上运行 MapReduce 作业时，将使用以下 Cmdlet。

* **Login-AzureRmAccount**：在 Azure 订阅中进行 Azure PowerShell 身份验证。

* **New-AzureRmHDInsightMapReduceJobDefinition**：使用指定的 MapReduce 信息创建新的*作业定义*。

* **Start-AzureRmHDInsightJob**：将作业定义发送到 HDInsight 并启动作业。 将返回作业对象。

* **Wait-AzureRmHDInsightJob**：使用作业对象来检查作业的状态。 它等到作业完成或超出等待时间。

* **Get-AzureRmHDInsightJobOutput**：用于检索作业的输出。

以下步骤演示了如何使用这些 Cmdlet 在 HDInsight 群集上运行作业。

1. 使用编辑器将以下代码保存为 **mapreducejob.ps1**。

    [!code-powershell[main](../../powershell_scripts/hdinsight/use-mapreduce/use-mapreduce.ps1?range=5-69)]

2. 打开一个新的 **Azure PowerShell** 命令提示符。 将目录更改为 **mapreducejob.ps1** 文件所在位置，然后使用以下命令来运行脚本：

        .\mapreducejob.ps1

    运行脚本时，系统会提示输入 HDInsight 群集的名称和该群集的登录名。 还会提示针对 Azure 订阅进行身份验证。

3. 作业完成后，将收到类似于以下文本的输出：

        Cluster         : CLUSTERNAME
        ExitCode        : 0
        Name            : wordcount
        PercentComplete : map 100% reduce 100%
        Query           :
        State           : Completed
        StatusDirectory : f1ed2028-afe8-402f-a24b-13cc17858097
        SubmissionTime  : 12/5/2014 8:34:09 PM
        JobId           : job_1415949758166_0071

    此输出指示作业已成功完成。

    > [!NOTE]
    > 如果 **ExitCode** 的值不是 0，请参阅[故障排除](#troubleshooting)。

    此示例还会将下载的文件存储到从中运行脚本的目录中的 **output.txt** 文件。

### <a name="view-output"></a>查看输出

若要查看作业生成的单词和计数，请在文本编辑器中打开 output.txt 文件。

> [!NOTE]
> MapReduce 作业的输出文件是固定不变的。 因此，如果重新运行此示例，将需要更改输出文件的名称。

## <a id="troubleshooting"></a>故障排除

如果作业完成时未返回任何信息，请查看该作业的错误。 要查看此作业的错误信息，请将以下命令添加到 **mapreducejob.ps1** 文件的末尾，保存，然后重新运行该文件。

```powershell
# Print the output of the WordCount job.
Write-Host "Display the standard output ..." -ForegroundColor Green
Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $wordCountJob.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

作业运行期间，此 cmdlet 返回写入到 STDERR 中的信息。

## <a id="summary"></a>摘要

如你所见，Azure PowerShell 提供了简单的方法，可在 HDInsight 群集上运行 MapReduce 作业、监视作业状态，以及检索输出。

## <a id="nextsteps"></a>后续步骤

有关 HDInsight 中的 MapReduce 作业的一般信息：

* [在 HDInsight Hadoop 上使用 MapReduce](hdinsight-use-mapreduce.md)

有关 HDInsight 上的 Hadoop 的其他使用方法的信息：

* [将 Hive 与 Hadoop on HDInsight 配合使用](hdinsight-use-hive.md)
* [将 Pig 与 Hadoop on HDInsight 配合使用](hdinsight-use-pig.md)

