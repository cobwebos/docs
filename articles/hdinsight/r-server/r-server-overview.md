---
title: Azure HDInsight 上的 ML Services 简介
description: 了解如何使用 HDInsight 上的 ML Services 创建用于大数据分析的应用程序。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: a8d164dd50ac190d2bc14fea70cde20bfdb89361
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85849920"
---
# <a name="what-is-ml-services-in-azure-hdinsight"></a>什么是 Azure HDInsight 中的 ML 服务

可以在 Azure 中创建 HDInsight 群集时选择使用 Microsoft Machine Learning Server 部署。 提供此选项的群集类型名为 ML Services  。 使用此功能可以按需访问 HDInsight 上的自适应分布式分析方法。

HDInsight 上的 ML 服务提供最新的功能，以用于针对几乎任何大小的数据集执行基于 R 的分析。 可将数据集加载到 Azure Blob 或 Data Lake Storage。 基于 R 的应用程序可以使用 8000 多个开源 R 包。 ScaleR 中的例程（Microsoft 的大数据分析包）同样可用。

边缘节点为连接到群集和运行 R 脚本提供了便捷的位置。 边缘节点允许跨服务器的核心运行 ScaleR 并行化分布式函数。 还可以通过使用 ScaleR 的 Hadoop Map Reduce 跨群集的各个节点运行这些函数。 此外，还可以使用 Apache Spark 计算上下文。

可以下载分析后生成的模型或预测，以便在本地使用。 也可在 Azure 中的其他位置对其进行`operationalized`。 具体而言，可以通过 [Azure 机器学习工作室（经典版）](https://studio.azureml.net)和 [Web 服务](../../machine-learning/studio/deploy-a-machine-learning-web-service.md)将其操作化。

## <a name="get-started-with-ml-services-on-hdinsight"></a>HDInsight 上的 ML Services 入门

若要在 HDInsight 中创建 ML 服务群集，请选择“ML 服务”群集类型。  ML 服务群集类型包括数据节点上的 ML Server，以及边缘节点。 边缘节点充当基于 ML 服务的分析的登陆区域。 有关如何创建群集的演练，请参阅[使用 Azure 门户创建 Apache Hadoop 群集](../hdinsight-hadoop-create-linux-clusters-portal.md)。

## <a name="why-choose-ml-services-in-hdinsight"></a>为什么选择 HDInsight 中的 ML Services？

HDInsight 中的 ML Services 具有下述优势：

### <a name="ai-innovation-from-microsoft-and-open-source"></a>通过 Microsoft 和开放源代码获得 AI 创新

  ML 服务包含高度自适应性的分布式算法集，例如 [RevoscaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler)、[revoscalepy](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/revoscalepy-package) 和 [microsoftML](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/microsoftml-package)。 这些算法可以处理超出物理内存大小的数据。 它们还能以分散的方式在各种平台上运行。 详细了解产品随附的 Microsoft 自定义 [R 包](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference)和 [Python 包](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference)集合。
  
  ML 服务将这些 Microsoft 创新和来自开源社区的贡献（R、Python 和 AI 工具包）联系在一起。 所有这些都在单个企业级平台上。 任何 R 或 Python 开放源代码机器学习包都可与来自 Microsoft 的任何专属创新配合运行。

### <a name="simple-secure-and-high-scale-operationalization-and-administration"></a>简单、安全且高度可缩放的操作和管理

  依赖于传统模式和环境的企业在操作化方面投入了许多时间和精力。 此措施会导致成本和延迟增大，因为这涉及到模型转换时间、让它们保持有效及最新状态的迭代工作、法规审批，以及管理权限。

  ML 服务提供企业级[操作化](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)。 在完成机器学习模型后，只需单击几下鼠标就能生成 Web 服务 API。 这些 [Web 服务](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services)托管在服务器网格或云中，并且可与业务线应用程序集成。 部署到弹性网格的能力可让你根据业务需求，针对批处理和实时评分无缝缩放。 有关说明，请参阅[使 HDInsight 上的 ML Services 可操作](r-server-operationalize.md)。

<!---
* **Deep ecosystem engagements to deliver customer success with optimal total cost of ownership**

  Individuals embarking on the journey of making their applications intelligent or simply wanting to learn the new world of AI and machine learning, need the right resources to help them get started. In addition to this documentation, Microsoft provides several learning resources and has engaged several training partners to help you ramp up and become productive quickly.
--->

> [!NOTE]  
> HDInsight 上的 ML Services 群集类型仅在 HDInsight 3.6 上受支持。 HDInsight 3.6 计划于 2020 年 12 月 31 日停用。

## <a name="key-features-of-ml-services-on-hdinsight"></a>HDInsight 上的 ML Services 的主要功能

HDInsight 上的 ML Services 包含以下功能。

| 功能类别 | 说明 |
|------------------|-------------|
| 支持 R | 适用于以 R 编写的解决方案的 [R 包](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference)，其中包含 R 的开源分发版和用于执行脚本的运行时基础结构。 |
| 支持 Python | 适用于以 Python 编写的解决方案的 [Python 模块](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference)，其中包含 Python 的开源分发版和用于执行脚本的运行时基础结构。
| [预先训练的模型](https://docs.microsoft.com/machine-learning-server/install/microsoftml-install-pretrained-models) | 适用于可视化分析和文本情绪分析，随时可用于对提供的数据进行评分。 |
| [部署和使用](r-server-operationalize.md) | `Operationalize`你的服务器，将解决方案部署为 Web 服务。 |
| [远程执行](r-server-hdinsight-manage.md#connect-remotely-to-microsoft-ml-services) | 在客户端工作站中，通过网络在 ML Services 群集 上启动远程会话。 |

## <a name="data-storage-options-for-ml-services-on-hdinsight"></a>适用于 HDInsight 上的 ML Services 的数据存储选项

HDFS 文件系统的默认存储可以是 Azure 存储帐户或 Azure Data Lake Storage。 在分析期间上传到群集存储的数据会持久保存。 即使在删除群集后，该数据也可供使用。 有多种工具可以处理将数据传输到存储。 这些工具包括存储帐户的基于门户的上传工具和 AzCopy 实用工具。

可以在创建群集期间启用对附加 Blob 和 Data Lake Store 的访问。 此功能不受限于使用的主存储选项。  要了解有关使用多个存储帐户的详细信息，请参阅[适用于 HDInsight 上的 ML Services 的 Azure 存储选项](./r-server-storage.md)一文。

也可以将 Azure 文件存储用作边缘节点上的存储选项。 使用 Azure 文件存储可以在 Linux 文件系统中启用 Azure 存储中创建的文件共享。 有关详细信息，请参阅[适用于 HDInsight 上的 ML 服务的 Azure 存储选项](r-server-storage.md)。

## <a name="access-ml-services-edge-node"></a>访问 ML Services 边缘节点

可以使用浏览器或 SSH/PuTTY 连接到边缘节点上的 Microsoft ML Server。 在创建群集期间，默认会安装 R 控制台。  

## <a name="develop-and-run-r-scripts"></a>开发和运行 R 脚本

R 脚本可以使用 8000 多个开源 R 包中的任何一个。 你还可以使用 ScaleR 库中的并行化分布式例程。 在边缘节点上运行的脚本将在该节点上的 R 解释器中运行。 但是，使用 Map Reduce (RxHadoopMR) 或 Spark (RxSpark) 计算上下文调用 ScaleR 函数的步骤除外。 这些函数将以分散的方式在与数据关联的各个数据节点上运行。 有关上下文选项的详细信息，请参阅[适用于 HDInsight 上的 ML 服务的计算上下文选项](r-server-compute-contexts.md)。

## <a name="operationalize-a-model"></a>`Operationalize`模型

完成数据建模后，可以在 Azure 中或本地`operationalize`模型，以便针对新数据执行预测。 此过程称为评分。 可以在 HDInsight、Azure 机器学习或本地进行评分。

### <a name="score-in-hdinsight"></a>在 HDInsight 中评分

若要在 HDInsight 中评分，请编写一个 R 函数。 该函数会调用你的模型，以针对加载到存储帐户中的新数据文件做出预测。 然后将预测保存回到存储帐户。 可以根据需要在群集的边缘节点上运行该例程，或使用计划作业来进行。

### <a name="score-in-azure-machine-learning-aml"></a>在 Azure 机器学习中评分 (AML)

若要使用 Azure 机器学习进行评分，请使用名为 [AzureML](https://cran.r-project.org/src/contrib/Archive/AzureML/) 的开放源代码 Azure 机器学习 R 包将模型发布为 Azure Web 服务。 为提供方便，此包已预装在边缘节点上。 接下来，使用 Azure 机器学习中的工具创建 Web 服务的用户界面，并根据需要调用 Web 服务进行评分。 然后，将 ScaleR 模型对象转换成等效的开源模型对象以用于 Web 服务。 使用 ScaleR 强制转换函数，例如适用于装配模型的 `as.randomForest()` 来完成转换。

### <a name="score-on-premises"></a>本地评分

若要在创建模型后进行本地评分，请使用 R 来序列化模型，将其下载，将其反序列化，然后使用它来为新数据评分。 可以使用前面“在 HDInsight 中评分”中所述的方法，或使用 [Web 服务](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services)对新数据进行评分。

## <a name="maintain-the-cluster"></a>维护群集

### <a name="install-and-maintain-r-packages"></a>安装和维护 R 包

由于 R 脚本的大多数步骤在边缘节点上运行，因此边缘节点上需要有大部分使用的 R 包。 若要在边缘节点上安装其他 R 包，可以在 R 中使用 `install.packages()` 方法。

如果只是使用 ScaleR 库例程，则通常不需要附加的 R 包。 可能需要对数据节点上的 rxExec 或 RxDataStep 执行使用附加的包。  

可以在创建群集之后，使用脚本操作来安装附加的包。 有关详细信息，请参阅[管理 HDInsight 群集中的 ML Services](r-server-hdinsight-manage.md)。

### <a name="change-apache-hadoop-mapreduce-memory-settings"></a>更改 Apache Hadoop MapReduce 内存设置

可以在 ML 服务运行 MapReduce 作业时修改其可用内存。 若要修改群集，请对群集使用 Apache Ambari UI。 有关 Ambari UI 的说明，请参阅[使用 Ambari Web UI 管理 HDInsight 群集](../hdinsight-hadoop-manage-ambari.md)。

可以在 RxHadoopMR 调用中使用 Hadoop 开关更改 ML 服务的可用内存： 

```r
hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"
```

### <a name="scale-your-cluster"></a>缩放群集

可以通过门户扩展或缩减现有的 HDInsight 上的 ML Services 群集。 通过纵向扩展，可以获得更多的容量来完成更大的处理任务。 可以在群集空闲时缩减其容量。 有关如何缩放群集的说明，请参阅[管理 HDInsight 群集](../hdinsight-administer-use-portal-linux.md)。

### <a name="maintain-the-system"></a>维护系统

在非工作时间，系统将在 HDInsight 群集中的基础 Linux VM 上执行 OS 维护。 通常，维护工作是在每个星期一和星期四的凌晨 3:30（VM 本地时间）执行的。 每次更新不会影响超过四分之一的群集。

在维护期间，正在运行的作业可能会变慢。 但是，这些作业应该都可运行完成。 除非发生需要重建群集的灾难性故障，否则任何自定义软件或本地数据在这些维护事件中都将保留。

## <a name="ide-options-for-ml-services-on-hdinsight"></a>适用于 HDInsight 上的 ML Services 的 IDE 选项

HDInsight 群集的 Linux 边缘节点是基于 R 的分析的登录区域。 最新版本的 HDInsight 在边缘节点上提供 RStudio Server 的基于浏览器的 IDE。 对于开发和执行而言，RStudio Server 的工作效率高于 R 控制台。

桌面 IDE 可以通过远程 MapReduce 或 Spark 计算上下文访问群集。 选项包括：Microsoft 的[针对 Visual Studio 的 R 工具](https://marketplace.visualstudio.com/items?itemName=MikhailArkhipov007.RTVS2019) (RTVS)、RStudio，以及 Walware 的基于 Eclipse 的 StatET。

通过在命令提示符下键入 R 来访问边缘节点上的 R 控制台。  使用控制台界面时，可以方便地在文本编辑器中开发 R 脚本。 然后，根据需要将脚本的部分剪切并粘贴到 R 控制台。

## <a name="pricing"></a>定价

ML 服务 HDInsight 群集相关价格的构成类似于其他 HDInsight 群集类型。 这些价格基于采用各种名称、数据和边缘节点的基础 VM 的大小。 此外还会加上核心运行小时数附加费。 有关详细信息，请参阅 [HDInsight 定价](https://azure.microsoft.com/pricing/details/hdinsight/)。

## <a name="next-steps"></a>后续步骤

若要详细了解如何在 HDInsight 群集上使用 ML 服务，请参阅以下文章：

* [使用 RStudio Server 在 Azure HDInsight 中的 ML Services 群集上执行 R 脚本](machine-learning-services-quickstart-job-rstudio.md)
* [适用于 HDInsight 上的 ML Services 群集的计算上下文选项](r-server-compute-contexts.md)
* [适用于 HDInsight 上的 ML Services 群集的存储选项](r-server-storage.md)
