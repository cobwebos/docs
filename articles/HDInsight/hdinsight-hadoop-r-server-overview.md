<!-- not suitable for Mooncake -->

<properties
	pageTitle="什么是 R on HDInsight？ HDInsight 上的 R Server（预览版）简介 | Azure"
	description="什么是 HDInsight 上的 R Server（预览版），以及如何使用 R Server 创建用于大数据分析的应用程序。"
	services="hdinsight"
	documentationCenter=""
	authors="jeffstokes72"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.date="06/01/2016"
	wacn.date=""/>


# HDInsight 上的 R Server（预览版）概述

自 Azure HDInsight 高级版推出后，你可以在 Azure 中创建 HDInsight 群集时选择使用 Microsoft R Server。这项新功能可让数据科研人员、统计人员和 R 程序员根据需要访问 HDInsight 上可缩放的分布式分析方法。

可依据手头的项目和任务调整群集大小，不再需要群集时将它解除。由于这些群集属于 Azure HDInsight 的一部分，提供企业级全年无休支持、99.9% 运行时间 SLA，并且能够灵活地与 Azure 生态系统中的其他组件集成。

>[AZURE.NOTE] 只有 HDInsight 高级版提供 R Server。目前 HDInsight 高级版只适用于 Hadoop 与 Spark 群集。因此，在 HDInsight 上目前只能配合 Hadoop 与 Spark 群集使用 R Server。有关详细信息，请参阅 [What are the different service levels and Hadoop components available with HDInsight?（HDInsight 提供哪些不同的服务级别和 Hadoop 组件？）](/documentation/articles/hdinsight-component-versioning-v1/)。

HDInsight 上的 R Server 提供最新的功能，可针对载入 Azure Blob 存储的大型数据集执行基于 R 的分析。由于 R Server 基于开放源代码的 R 构建，因此你构建的基于 R 的应用程序可以利用超过 8000 个任意开放源代码 R 包，以及 ScaleR（R Server 附带的 Microsoft 的大数据分析包）的例程。

高级群集的边缘节点为连接到群集和运行 R 脚本提供了便捷的位置。使用边缘节点，你可以选择跨边缘节点服务器的各个核心运行 ScaleR 的并行化分布式函数。你还可以选择通过使用 ScaleR 的 Hadoop Map Reduce 或 Spark 计算上下文跨群集的各个节点运行这些函数。

可以下载分析后生成的模型或预测，以便在本地使用。也可以在 Azure 中的其他位置（例如通过 [Azure Machine Learning Studio](http://studio.azureml.net) [Web 服务](/documentation/articles/machine-learning-publish-a-machine-learning-web-service/)）操作这些模型或预测。

## HDInsight 上的 R 入门

若要在 HDInsight 群集中包含 R Server，必须在通过 Azure 门户创建群集时使用“高级”选项创建 Hadoop 或 Spark 群集。这两种群集类型使用相同的配置，其中包括群集数据节点上的 R Server，以及作为基于 R Server 的分析登录区域的边缘节点。请参阅 [Getting Started with R Server on HDInsight（HDInsight 上的 R Server 入门）](/documentation/articles/hdinsight-hadoop-r-server-get-started/)了解创建群集的详细演练。

## 了解数据存储选项

HDInsight 群集的默认存储是 Blob 存储，其中的 HDFS 文件系统已映射到 Blob 容器。这可确保在分析过程中，上载到群集存储或写入群集存储的任何数据均会持久保存。可以使用 [AzCopy](/documentation/articles/storage-use-azcopy/) 实用工具向/从 Blob 复制数据。

除了使用 Blob 存储以外，你还可以选择将 [Azure Data Lake Storage](/home/features/data-lake-store/) 用于群集。如果使用 Data Lake，则可针对 HDFS 存储同时使用 Blob 存储和 Data Lake。

你也可以将 [Azure 文件](/documentation/articles/storage-how-to-use-files-linux/)服务用作边缘节点上的存储选项。Azure 文件可让你将 Azure 存储空间中创建的文件共享装载到 Linux 文件系统。有关适用于 HDInsight 群集上 R Server 的数据存储选项的详细信息，请参阅 [Storage options for R Server on HDInsight clusters（适用于 HDInsight 群集上 R Server 的存储选项）](/documentation/articles/hdinsight-hadoop-r-server-storage/)。

## 访问群集上的 R Server

创建包含 R Server 的群集后，可以通过 SSH/PuTTY 连接到群集边缘节点的 R 控制台。如果你选择在边缘节点上安装可选的 RStudio Server IDE，则还可以通过浏览器进行连接。有关安装 RStudio Server 的详细信息，请参阅 [Installing RStudio Server on HDInsight clusters（在 HDInsight 群集上安装 RStudio Server）](/documentation/articles/hdinsight-hadoop-r-server-install-r-studio/)。

## 开发和运行 R 脚本

创建和运行的 R 脚本可以使用 8000 多种开放源代码 R 包中的任何一种，此外，还可以使用 ScaleR 库中的并行化分布式例程。一般而言，在边缘节点上的 R Server 中运行的脚本将在该节点上的 R 解释程序内运行，但调用计算上下文设置为 Hadoop Map Reduce (RxHadoopMR) 或 Spark (RxSpark) 的某个 ScaleR 函数的步骤除外。

在这种情况下，函数将以分布方式跨与引用数据关联的群集的数据（任务）节点运行。有关不同计算上下文选项的详细信息，请参阅 [Compute context options for R Server on HDInsight premium（适用于 HDInsight 高级版上的 R Server 的计算上下文选项）](/documentation/articles/hdinsight-hadoop-r-server-compute-contexts/)。

## 操作模型

完成数据建模后，可以在 Azure 中和本地操作模型，以便针对新数据执行预测。此过程称为评分。以下提供了一些示例：

### 在 HDInsight 中评分

若要在 HDInsight 中评分，可以针对已载入存储帐户的新数据文件编写调用模型的 R 函数以进行预测。然后将预测保存回到存储帐户。可以根据需要在群集的边缘节点上运行该例程，或使用计划作业来进行。

### 在 Azure 机器学习中评分

若要使用 Azure 机器学习 Web 服务进行评分，可以使用[开放源代码 Azure 机器学习 R 包](http://www.inside-r.org/blogs/2015/11/18/enhancements-azureml-package-connect-r-azureml-studio)[将模型发布为 Azure Web 服务](http://www.r-bloggers.com/deploying-a-car-price-model-using-r-and-azureml/)。接下来，使用 Azure 机器学习中的工具创建 Web 服务的用户界面，然后根据需要调用 Web 服务进行评分。

如果选择此选项，则需要将所有 ScaleR 模型对象转换成对等的开放源代码模型对象，才可配合 Web 服务使用。这可以通过使用 ScaleR 强制转换函数完成，例如，适用于装配模型的 `as.randomForest()`。

### 本地评分

若要在创建模型之后进行本地评分，可以在 R 中序列化模型，将其下载，将其反序列化，然后使用它进行新数据评分。可以使用前面[在 HDInsight 中评分](#scoring-in-hdinsight)所述的方法，或使用 [DeployR](https://deployr.revolutionanalytics.com/) 进行新数据评分。

## 维护群集

### 安装和维护 R 包

由于大多数 R 脚本在边缘节点上运行，因此边缘节点上需要有大部分使用的 R 包。若要在边缘节点上安装其他 R 包，可以在 R 中使用常用的 `install.packages()` 方法。

在大多数情况下，如果你只需要从 ScaleR 库跨群集使用例程，则不需要在数据节点上安装其他 R 包。但是，你可能需要其他包才能支持在数据节点上使用 **rxExec** 或 **RxDataStep** 执行。

在这种情况下，必须在创建群集之后，使用脚本操作来指定其他包。有关详细信息，请参阅 [Creating an HDInsight cluster with R Server（创建包含 R Server 的 HDInsight 群集）](/documentation/articles/hdinsight-hadoop-r-server-get-started/)。

### 更改 Hadoop Map Reduce 内存设置

可以在运行 Map Reduce 作业时修改群集，以更改 R Server 的可用内存量。若要修改群集，可以通过群集的 Azure 门户边栏选项卡使用 Apache Ambari UI。有关如何访问群集的 Ambari UI 的说明，请参阅 [Manage HDInsight clusters using the Ambari Web UI（使用 Ambari Web UI 管理 HDInsight 群集）](/documentation/articles/hdinsight-hadoop-manage-ambari/)。

也可以在 **RxHadoopMR** 的调用中使用 Hadoop 参数更改 R Server 可用的内存量，如下所示：

	hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### 缩放你的群集

可以通过门户扩展或缩减现有群集。通过缩放，可以获得更多的容量来完成较大的处理任务，或者在群集空闲时缩减容量。有关如何缩放群集的说明，请参阅 [Manage HDInsight clusters（管理 HDInsight 群集）](/documentation/articles/hdinsight-administer-use-portal-linux/)。

### 维护系统

在非工作时间，系统将在 HDInsight 群集的基础 Linux VM 上执行维护，以应用 OS 修补程序和其他更新。通常，维护操作将在星期一和星期四凌晨 3:30（基于 VM 的本地时间）完成。执行更新时，每次应该只有不到四分之一的群集会受影响。

由于头节点是冗余的，且并非所有数据节点都受影响，因此在此时间段运行的所有作业可能会变慢。但是，这些作业应该都可运行完成。除非发生需要重建群集的灾难性故障，否则任何自定义软件或本地数据在这些维护事件中都将保留。

## 了解适用于 HDInsight 群集上 R Server 的 IDE 选项

HDInsight 高级版群集的 Linux 边缘节点是基于 R 的分析的登录区域。连接到群集之后，可在 Linux 命令提示符下键入“R”，启动 R Server 的控制台界面。如果在另一个窗口中运行 R 脚本开发的文本编辑器，可根据需要将脚本部分剪切并粘贴到 R 控制台，以使用增强的控制台界面。

用于 R 脚本开发的更高级工具是在桌面上使用基于 R 的 IDE，例如，Microsoft 最近推出的 [R Tools for Visual Studio](https://www.visualstudio.com/features/rtvs-vs.aspx) (RTVS)。它是 [RStudio](https://www.rstudio.com/products/rstudio-server/) 的桌面与服务器工具系列。你也可以使用 Walware 的基于 Eclipse 的 [StatET](http://www.walware.de/goto/statet)。

另一种做法是在 Linux 边缘节点自身上安装 IDE。一个常见选项是 [RStudio Server](https://www.rstudio.com/products/rstudio-server/)，它为远程客户端提供基于浏览器的 IDE。在 HDInsight 高级版群集的边缘节点上安装 RStudio Server 可以提供完整的 IDE 体验，以便通过群集上的 R Server 开发和运行 R 脚本。与 R 控制台相比，它可以大幅提高生产力。如果你要使用 RStudio Server，请参阅 [Installing RStudio Server on HDInsight clusters（在 HDInsight 群集上安装 RStudio Server）](/documentation/articles/hdinsight-hadoop-r-server-install-r-studio/)。

## 了解定价

包含 R Server 的 HDInsight 高级版群集的相关费用结构与标准 HDInsight 群集类似。这些费用以各种名称、数据和边缘节点的基础 VM 大小为基准，加上高级版的核心运行小时数附加费。有关 HDInsight 高级版定价，包括公共预览期定价及 30 天免费试用的详细信息，请参阅 [HDInsight pricing（HDInsight 定价](/pricing/details/hdinsight/)）。

## 后续步骤

单击以下链接详细了解如何使用 HDInsight 群集上的 R Server。

- [Getting Started with R Server on HDInsight（HDInsight 上的 R Server 入门）](/documentation/articles/hdinsight-hadoop-r-server-get-started/)

- [Add RStudio Server to HDInsight Premium（将 RStudio Server 添加到 HDInsight 高级版）](/documentation/articles/hdinsight-hadoop-r-server-install-r-studio/)

- [用于 HDInsight（预览版）上的 R Server 的计算上下文选项](/documentation/articles/hdinsight-hadoop-r-server-compute-contexts/)

- [Azure Storage options for R Server on HDInsight Premium（适用于 HDInsight 高级版上的 R Server 的 Azure 存储空间选项）](/documentation/articles/hdinsight-hadoop-r-server-storage/)

<!---HONumber=Mooncake_0725_2016-->