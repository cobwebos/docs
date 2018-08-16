---
title: 在 Hadoop on HDInsight 中使用 Windows 电脑 - Azure | Microsoft Docs - Azure
description: 操作 Hadoop on HDInsight 中的 Windows 电脑。 使用 PowerShell、Visual Studio 和 Linux 工具管理与查询群集。 使用 .NET 开发大数据解决方案。
services: hdinsight
author: jasonwhowell
ms.author: jasonh
ms.topic: conceptual
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 05/17/2017
ms.openlocfilehash: a3fbd9cca038547e92c034577042aebbac6fc1fe
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/07/2018
ms.locfileid: "39593054"
---
# <a name="work-in-the-hadoop-ecosystem-on-hdinsight-from-a-windows-pc"></a>使用 Windows 电脑在 HDInsight 上的 Hadoop 生态系统中工作

了解 Windows 电脑上用于在 HDInsight 的 Hadoop 生态系统中工作的开发和管理选项。 

HDInsight 基于在 Linux 上开发的 Apache Hadoop 和 Hadoop 组件与开源技术。 HDInsight 3.4 及更高版本使用 Ubuntu Linux 发行版作为群集的基础 OS。 但是，可以通过 Windows 客户端或 Windows 开发环境使用 HDInsight。

## <a name="use-powershell-for-deployment-and-management-tasks"></a>使用 PowerShell 完成部署和管理任务
Azure PowerShell 是一个脚本编写环境，使用它可以通过 Windows 在 HDInsight 中控制和自动完成部署与管理任务。

可以使用 PowerShell 完成的任务示例：

* [使用 PowerShell 创建群集](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [使用 PowerShell 运行 Hive 查询](hadoop/apache-hadoop-use-hive-powershell.md)
* [使用 PowerShell 管理群集](hdinsight-administer-use-powershell.md)

请遵循[安装和配置 Azure Powershell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) 的步骤来获取最新版本。 如果脚本需要修改后才能使用 Azure 资源管理器的新 cmdlet，请参阅[迁移到基于 Azure 资源管理器的面向 HDInsight 群集的开发工具](hdinsight-hadoop-development-using-azure-resource-manager.md)。

## <a name="utilities-you-can-run-in-a-browser"></a>可在浏览器中运行的实用工具
以下实用工具提供可在浏览器中运行的 Web UI：
* [Azure Cloud Shell（预览）](https://docs.microsoft.com/azure/cloud-shell/quickstart)是可在浏览器和 Azure 门户中运行的交互式命令行 shell。
* [Ambari Web UI](hdinsight-hadoop-manage-ambari.md) 是 Azure 门户中提供的管理和监视实用工具，可用于管理不同类型的作业，例如：
    * [将 Ambari 与 REST API 配合使用](hdinsight-hadoop-manage-ambari-rest-api.md)
    * [Ambari 中的 Hive 视图](hadoop/apache-hadoop-use-hive-ambari-view.md)
    * [Ambari 中的 Tez 视图](hdinsight-debug-ambari-tez-view.md)

## <a name="data-lake-hadoop-tools-for-visual-studio"></a>用于 Visual Studio 的 Data Lake (Hadoop) 工具
使用用于 Visual Studio 的 Data Lake 工具可以部署和管理 Storm 拓扑。 Data Lake 工具还会安装 SCP.NET SDK 用于通过 Visual Studio 开发 C# Storm 拓扑。

在转到下面的示例之前，请[安装并试用用于 Visual Studio 的 Data Lake 工具](hadoop/apache-hadoop-visual-studio-tools-get-started.md)。 

可以使用用于 Visual Studio 的 Data Lake 工具完成的任务示例：
* [通过 Visual Studio 部署和管理 Storm 拓扑](storm/apache-storm-deploy-monitor-topology-linux.md)
* [使用 Visual Studio 开发 Storm 的 C# 拓扑](storm/apache-storm-develop-csharp-visual-studio-topology.md) 软件包中包含可连接到数据库（例如 Azure Cosmos DB 和 SQL 数据库）的 Storm 拓扑的示例模板。

## <a name="visual-studio-and-the-net-sdk"></a>Visual Studio 和 .NET SDK 

可以配合使用 Visual Studio 和 .NET SDK 来管理群集及开发大数据应用程序。 可将其他 IDE 用于以下任务，但示例显示在 Visual Studio 中。

可在 Visual Studio 中使用 .NET SDK 完成的任务示例：
* [通过 .NET Framework 应用程序创建群集和使用 HDInsight](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)
* [使用 .NET SDK 运行 Hive 查询](hadoop/apache-hadoop-use-hive-dotnet-sdk.md)
* [在 Hadoop 上将 C# 用户定义的函数与 Hive 和 Pig 流式处理配合使用](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

> 提示：如果在基于 Windows 的 HDInsight 群集中运行 .NET 解决方案，现在正好可以计划迁移到基于 Linux 的群集。 有关详细信息，请参阅[将基于 Windows 的 HDInsight 的 .NET 解决方案迁移到基于 Linux 的 HDInsight](hdinsight-hadoop-migrate-dotnet-to-linux.md)。

## <a name="intellij-idea-and-eclipse-ide-for-spark-clusters"></a>用于 Spark 群集的 Intellij IDEA 和 Eclipse IDE
[Intellij IDEA](https://www.jetbrains.com/idea/download) 和 [Eclipse IDE](https://www.eclipse.org/downloads/) 都可用于：
* 在 HDInsight Spark 群集中开发和提交 Scala Spark 应用程序。
* 访问 Spark 群集资源。
* 本地开发和运行 Scala Spark 应用程序。

以下文章介绍了相关信息： 
* Intellij IDEA：[使用用于 Intellij 的 Azure 工具包插件和 Scala SDK 创建 Spark 应用程序。](spark/apache-spark-intellij-tool-plugin.md)
* 用于 Eclipse 的 Eclipse IDE 或 Scala IDE：[创建 Spark 应用程序和用于 Eclipse 的 Azure 工具包](spark/apache-spark-eclipse-tool-plugin.md) 


## <a name="notebooks-on-spark-for-data-scientists"></a>Spark 上面向数据科研人员的 Notebook 
HDInsight 中的 Apache Spark 群集包含可与 Jupyter Notebook 配合使用的 Zeppelin Notebook 和内核。 

* [了解如何配合 Spark 群集上的内核和 Jupyter Notebook 来测试 Spark 应用程序](spark/apache-spark-zeppelin-notebook.md)
* [了解如何使用 Spark 群集上的 Zeppelin Notebook 来运行 Spark 作业](spark/apache-spark-jupyter-notebook-kernels.md) 


## <a name="run-linux-based-tools-and-technologies-on-windows"></a>在 Windows 上运行基于 Linux 的工具和技术

如果在某种情况下，必须使用的某种工具或技术只能在 Linux 上使用，请考虑以下选项：

* **Windows 10 上的 bash (beta)** 在 Windows 上提供一个 Linux 子系统。 Bash 允许直接运行 Linux 实用工具，而无需维护专用的 Linux 安装。 [在 Windows 10 上安装和运行 Bash beta](https://msdn.microsoft.com/commandline/wsl/install_guide)
* 使用**适用于 Windows 的 Docker** 可以访问许多基于 Linux 的工具，可以直接从 Windows 运行。 例如，可以直接在 Windows 中使用 Docker 来运行适用于 Hive 的 Beeline 客户端。 还可以使用 Docker 运行本地 Jupyter Notebook，以及远程连接到 Spark on HDInsight。 [适用于 Windows 的 Docker 入门](https://docs.docker.com/docker-for-windows/)
* 使用 **[MobaXTerm](http://mobaxterm.mobatek.net/)** 可以通过 SSH 连接以图形方式浏览群集文件系统。

## <a name="next-steps"></a>后续步骤
如果不太熟悉如何在基于 Linux 的群集中操作，请参阅以下文章：
* [设置 Hadoop、Kafka、Spark 或其他群集](hdinsight-hadoop-provision-linux-clusters.md)
* [有关 Linux 上的 HDInsight 群集的提示](hdinsight-hadoop-linux-information.md)
