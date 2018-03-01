---
title: "在 HDInsight 中将 Hadoop Hive 与 PowerShell 配合使用 — Azure | Microsoft Docs"
description: "使用 PowerShell 在 HDInsight 上的 Hadoop 中运行 Hive 查询。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: cb795b7c-bcd0-497a-a7f0-8ed18ef49195
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/26/2018
ms.author: larryfr
ms.openlocfilehash: fbd5ad2aedf0c3022e702a63f8e3d12735b41313
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
---
# <a name="run-hive-queries-using-powershell"></a>使用 PowerShell 运行 Hive 查询
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

本文档提供使用 Azure 资源组模式中的 Azure PowerShell 在 HDInsight 群集上的 Hadoop 中运行 Hive 查询的示例。

> [!NOTE]
> 本文档未详细描述示例中使用的 HiveQL 语句的作用。 有关此示例中使用的 HiveQL 的信息，请参阅[将 Hive 与 HDInsight 上的 Hadoop 配合使用](hdinsight-use-hive.md)。

## <a name="prerequisites"></a>先决条件

* 基于 Linux 的 Hadoop on HDInsight 群集版本 3.4 或更高版本。

  > [!IMPORTANT]
  > Linux 是 HDInsight 3.4 或更高版本上使用的唯一操作系统。 有关详细信息，请参阅 [HDInsight 在 Windows 上停用](../hdinsight-component-versioning.md#hdinsight-windows-retirement)。

* 具有 Azure PowerShell 的客户端。

[!INCLUDE [upgrade-powershell](../../../includes/hdinsight-use-latest-powershell.md)]

## <a name="run-a-hive-query"></a>运行 Hive 查询

Azure PowerShell 提供 *cmdlet*，可在 HDInsight 上远程运行 Hive 查询。 cmdlet 在内部对 HDInsight 群集上的 [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) 进行 REST 调用。

在远程 HDInsight 群集上运行 Hive 查询时，将使用以下 Cmdlet：

* `Add-AzureRmAccount`：在 Azure 订阅中进行 Azure PowerShell 身份验证。
* `New-AzureRmHDInsightHiveJobDefinition`：使用指定的 HiveQL 语句创建“作业定义”。
* `Start-AzureRmHDInsightJob`：将作业定义发送到 HDInsight 并启动作业。 将返回作业对象。
* `Wait-AzureRmHDInsightJob`：使用作业对象来检查作业的状态。 它等到作业完成或超出等待时间。
* `Get-AzureRmHDInsightJobOutput`：用于检索作业的输出。
* `Invoke-AzureRmHDInsightHiveJob`：用于运行 HiveQL 语句。 此 cmdlet 将阻止查询完成，然后返回结果。
* `Use-AzureRmHDInsightCluster`：设置要用于 `Invoke-AzureRmHDInsightHiveJob` 命令的当前群集。

以下步骤演示了如何使用这些 Cmdlet 在 HDInsight 群集上运行作业：

1. 使用编辑器将以下代码保存为 `hivejob.ps1`。

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=5-42)]

2. 打开一个新的 **Azure PowerShell** 命令提示符。 将目录更改到 `hivejob.ps1` 文件的位置，并使用以下命令来运行脚本：

        .\hivejob.ps1

    脚本运行时，系统会提示输入群集名称和该群集的 HTTPS/管理员帐户凭据。 可能还会提示登录到 Azure 订阅。

3. 作业完成时，它会返回类似以下文本的信息：

        Display the standard output...
        2012-02-03      18:35:34        SampleClass0    [ERROR] incorrect       id
        2012-02-03      18:55:54        SampleClass1    [ERROR] incorrect       id
        2012-02-03      19:25:27        SampleClass4    [ERROR] incorrect       id

4. 如前所述，`Invoke-Hive` 可以用来运行查询，并等待响应。 使用以下脚本查看 Invoke-Hive 的工作原理：

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=50-71)]

    输出类似于以下文本：

        2012-02-03    18:35:34    SampleClass0    [ERROR]    incorrect    id
        2012-02-03    18:55:54    SampleClass1    [ERROR]    incorrect    id
        2012-02-03    19:25:27    SampleClass4    [ERROR]    incorrect    id

   > [!NOTE]
   > 对于较长的 HiveQL 查询，可以使用 Azure PowerShell **Here-Strings** cmdlet 或 HiveQL 脚本文件。 以下代码段显示了如何使用 `Invoke-Hive` cmdlet 来运行 HiveQL 脚本文件。 HiveQL 脚本文件必须上传到 wasb://。
   >
   > `Invoke-AzureRmHDInsightHiveJob -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
   >
   > 有关 **Here-Strings** 的详细信息，请参阅<a href="http://technet.microsoft.com/library/ee692792.aspx" target="_blank">使用 Windows PowerShell Here-Strings</a>。

## <a name="troubleshooting"></a>故障排除

如果作业完成时未返回任何信息，请查看错误日志。 若要查看此作业的错误信息，请将以下内容添加到 `hivejob.ps1` 文件的末尾，保存并重新运行该文件。

```powershell
# Print the output of the Hive job.
Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

作业处理期间，此 cmdlet 返回写入到 STDERR 中的信息。

## <a name="summary"></a>摘要

如你所见，Azure PowerShell 提供了简单的方法让你在 HDInsight 群集上运行 Hive 查询，监视作业状态，以及检索输出。

## <a name="next-steps"></a>后续步骤

有关 HDInsight 中的 Hive 的一般信息：

* [将 Hive 与 Hadoop on HDInsight 配合使用](hdinsight-use-hive.md)

有关 HDInsight 上的 Hadoop 的其他使用方法的信息：

* [将 Pig 与 Hadoop on HDInsight 配合使用](hdinsight-use-pig.md)
* [将 MapReduce 与 HDInsight 上的 Hadoop 配合使用](hdinsight-use-mapreduce.md)
