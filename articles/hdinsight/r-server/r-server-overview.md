---
title: Azure HDInsight 上的 ML Services 简介 | Microsoft Docs
description: 了解如何使用 HDInsight 上的 ML Services 创建用于大数据分析的应用程序。
services: hdinsight
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.assetid: 6dc21bf5-4429-435f-a0fb-eea856e0ea96
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: nitinme
ms.openlocfilehash: c952d64229102f99dde393bc9a6efda45fc1c717
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2018
ms.locfileid: "39358845"
---
# <a name="introduction-to-ml-services-and-open-source-r-capabilities-on-hdinsight"></a>HDInsight 上的 ML Services 和开放源代码 R 功能简介

> [!NOTE]
> 2017 年 9 月，Microsoft R Server 以 Microsoft Machine Learning Server 或 ML Server 的新名称发布。 因此，HDInsight 上的 R Server 群集现称为 HDInsight 上的机器学习服务或 ML Services 群集。 有关 R Server 名称更改的详细信息，请参阅 [Microsoft R Server 现在是 Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/rebranding-microsoft-r-server#get-support-for-r-server)。

可以在 Azure 中创建 HDInsight 群集时选择使用 Microsoft Machine Learning Server 部署。 提供此选项的群集类型名为 ML Services。 这项功能可让数据科研人员、统计人员和 R 程序员根据需要访问 HDInsight 上可缩放的分布式分析方法。

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

HDInsight 上的 ML Services 提供最新的功能，可针对载入 Azure Blob 或 Data Lake 存储的几乎任何大小的数据集执行基于 R 的分析。 由于 ML Services 群集基于开放源代码 R 构建，因此，构建的基于 R 的应用程序可以利用超过 8000 个任意开放源代码 R 包。 ScaleR 中的例程（Microsoft 的大数据分析包）同样可用。

群集的边缘节点为连接到群集和运行 R 脚本提供了便捷的位置。 使用边缘节点，可以选择跨边缘节点服务器的各个核心运行 ScaleR 的并行化分布式函数。 还可以选择通过使用 ScaleR 的 Hadoop Map Reduce 或 Spark 计算上下文跨群集的各个节点运行这些函数。

可以下载分析后生成的模型或预测，以便在本地使用。 也可以在 Azure 中的其他位置（由其是通过 [Azure 机器学习工作室](http://studio.azureml.net) [Web 服务](../../machine-learning/studio/publish-a-machine-learning-web-service.md)）操作这些模型。

## <a name="get-started-with-ml-services-on-hdinsight"></a>HDInsight 上的 ML Services 入门

若要在 Azure HDInsight 中创建 ML Services 群集，请在使用 Azure 门户创建 HDInsight 群集时选择 ML Services 群集类型。 ML Services 群集类型包括群集数据节点以及作为基于 ML Services 的分析登录区域的边缘节点上的 ML Services。 请参阅 [HDInsight 上的 ML Services 入门](r-server-get-started.md)了解创建群集的详细演练。

## <a name="why-choose-ml-services-in-hdinsight"></a>为什么选择 HDInsight 中的 ML Services？

HDInsight 中的 ML Services 具有下述优势：

### <a name="ai-innovation-from-microsoft-and-open-source"></a>通过 Microsoft 和开放源代码获得 AI 创新

  ML Services 包括一组高度可缩放分布式算法，例如 [RevoscaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler)、[revoscalepy](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/revoscalepy-package) 和 [microsoftML](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/microsoftml-package)，可用于处理超出物理内存大小的数据，并且以分布方式在各种平台上运行。 详细了解产品随附的 Microsoft 自定义 [R 包](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference)和 [Python 包](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference)集合。
  
  ML Services 在单个企业级平台上，将这些 Microsoft 创新和来自开放源代码社区的贡献（R、Python 和 AI 工具包）联系在一起。 任何 R 或 Python 开放源代码机器学习包都可与来自 Microsoft 的任何专属创新配合运行。

### <a name="simple-secure-and-high-scale-operationalization-and-administration"></a>简单、安全且高度可缩放的操作和管理

  依赖于传统模式和环境的企业在操作化方面投入了许多时间和精力。 这导致成本和延迟增大，包括：模型转换时间、让它们保持有效及最新状态的迭代工作、法规审批、通过操作化管理权限。

  ML Services 提供企业级[操作化](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)，这体现在完成机器学习模型后，只需单击几下鼠标就能生成 Web 服务 API。 这些 [Web 服务](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services)托管在服务器网格或云中，并且可与业务线应用程序集成。 部署到弹性网格的能力可让你根据业务需求，针对批处理和实时评分无缝缩放。 有关说明，请参阅[使 HDInsight 上的 ML Services 可操作](r-server-operationalize.md)。

<!---
* **Deep ecosystem engagements to deliver customer success with optimal total cost of ownership**

  Individuals embarking on the journey of making their applications intelligent or simply wanting to learn the new world of AI and machine learning, need the right resources to help them get started. In addition to this documentation, Microsoft provides several learning resources and has engaged several training partners to help you ramp up and become productive quickly.
--->

## <a name="key-features-of-ml-services-on-hdinsight"></a>HDInsight 上的 ML Services 的主要功能

HDInsight 上的 ML Services 包含以下功能。

| 功能类别 | Description |
|------------------|-------------|
| 支持 R | 适用于以 R 编写的解决方案的 [R 包](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference)，R 开源分发版和用于执行脚本的运行时基础结构。 |
| 支持 Python | 适用于以 Python 编写的解决方案的 [Python 模块](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference)，Python 开源分发版和用于执行脚本的运行时基础结构。
| [预先训练的模型](https://docs.microsoft.com/machine-learning-server/install/microsoftml-install-pretrained-models) | 适用于可视化分析和文本情绪分析，随时可用于对提供的数据进行评分。 |
| [部署和使用](r-server-operationalize.md) | 使服务器可操作，将解决方案部署为 Web 服务。 |
| [远程执行](r-server-hdinsight-manage.md#connect-remotely-to-microsoft-ml-services) | 在客户端工作站中，通过网络在 ML Services 群集 上启动远程会话。 |


## <a name="data-storage-options-for-ml-services-on-hdinsight"></a>适用于 HDInsight 上的 ML Services 的数据存储选项

HDInsight 群集的 HDFS 文件系统的默认存储可以与 Azure 存储帐户或 Azure Data Lake Store 相关联。 这种关联可确保在分析过程中，上传到群集存储的任何数据均会持久保存，即使在删除群集后，数据也可供使用。 对于所选择的将数据传输到存储的选项有各种工具，包括存储帐户的基于门户的上传工具和 [AzCopy](../../storage/common/storage-use-azcopy.md) 实用程序。

无论选择哪个来充当主存储，都可以在群集预配过程中选择启用对附加 Blob 和 Data Lake Store 的访问权限。 有关向额外帐户授予访问权限的信息，请参阅 [HDInsight 上的 ML Services 入门](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-get-started)。 要了解有关使用多个存储帐户的详细信息，请参阅[适用于 HDInsight 上的 ML Services 的 Azure 存储选项](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-storage)一文。

也可以将 [Azure 文件](../../storage/files/storage-how-to-use-files-linux.md)服务用作边缘节点上的存储选项。 Azure 文件可让你将 Azure 存储中创建的文件共享装载到 Linux 文件系统。 若要深入了解 HDInsight 上的 ML Services 群集的数据存储选项，请参阅 [适用于 HDInsight 上的 ML Services 的 Azure 存储选项](r-server-storage.md)。

## <a name="access-ml-services-edge-node"></a>访问 ML Services 边缘节点

可以使用浏览器连接到边缘节点上的 Microsoft ML Server。 它是在群集创建过程中默认安装的。 有关详细信息，请参阅 [HDInsight 上的 ML Services 入门](r-server-get-started.md)。 还可以使用 SSH/PuTTY 通过命令行连接到群集边缘节点，以访问 R 控制台。

## <a name="develop-and-run-r-scripts"></a>开发和运行 R 脚本

创建和运行的 R 脚本可以使用 8000 多种开放源代码 R 包中的任何一种，此外，还可以使用 ScaleR 库中可用的并行化分布式例程。 一般而言，使用边缘节点上的 ML Services 运行的脚本将在该节点上的 R 解释程序内运行。 但需要调用计算上下文设置为 Hadoop Map Reduce (RxHadoopMR) 或 Spark (RxSpark) 的 ScaleR 函数的这些步骤除外。 在这种情况下，函数将以分布方式跨与引用数据关联的群集的数据（任务）节点运行。 有关不同计算上下文选项的详细信息，请参阅[适用于 HDInsight 上的 ML Services 的计算上下文选项](r-server-compute-contexts.md)。

## <a name="operationalize-a-model"></a>操作模型

完成数据建模后，可以在 Azure 中或本地操作模型，以便针对新数据执行预测。 此过程称为评分。 可以在 HDInsight、Azure 机器学习或本地进行评分。

### <a name="score-in-hdinsight"></a>在 HDInsight 中评分

若要在 HDInsight 中评分，可以针对已载入存储帐户的新数据文件编写调用模型的 R 函数以进行预测。 然后将预测保存回到存储帐户。 可以根据需要在群集的边缘节点上运行该例程，或使用计划作业来进行。

### <a name="score-in-azure-machine-learning-aml"></a>在 Azure 机器学习中评分 (AML)

若要使用 Azure 机器学习进行评分，请使用名为 [AzureML](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html) 的开放源代码 Azure 机器学习 R 包将模型发布为 Azure Web 服务。 为提供方便，此包已预装在边缘节点上。 接下来，使用 Azure 机器学习中的工具创建 Web 服务的用户界面，并根据需要调用 Web 服务进行评分。

如果选择此选项，则必须将所有 ScaleR 模型对象转换成对等的开放源代码模型对象，才可配合 Web 服务使用。 使用 ScaleR 强制转换函数，例如适用于装配模型的 `as.randomForest()` 来完成转换。

### <a name="score-on-premises"></a>本地评分

要在创建模型之后进行本地评分，可以在 R 中序列化模型，将其下载，将其反序列化，然后使用它进行新数据评分。 可以使用前面[在 HDInsight 中评分](#scoring-in-hdinsight)所述的方法，或使用 [Web 服务](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services)对新数据进行评分。

## <a name="maintain-the-cluster"></a>维护群集

### <a name="install-and-maintain-r-packages"></a>安装和维护 R 包

由于 R 脚本的大多数步骤在边缘节点上运行，因此边缘节点上需要有大部分使用的 R 包。 若要在边缘节点上安装其他 R 包，可以在 R 中使用 `install.packages()` 方法。

如果正在群集中使用 ScaleR 库中的例程，则通常不需要在数据节点上安装其他 R 包。 但是，可能需要其他包才能支持在数据节点上使用 rxExec 或 RxDataStep 执行。

在这种情况下，可以在创建群集之后，使用脚本操作来安装其他包。 有关详细信息，请参阅[管理 HDInsight 群集中的 ML Services](r-server-hdinsight-manage.md)。

### <a name="change-hadoop-mapreduce-memory-settings"></a>更改 Hadoop MapReduce 内存设置

可以在运行 MapReduce 作业时修改群集，以更改 ML Services 的可用内存量。 若要修改群集，可以通过群集的 Azure 门户边栏选项卡使用 Apache Ambari UI。 有关如何访问群集的 Ambari UI 的说明，请参阅[使用 Ambari Web UI 管理 HDInsight 群集](../hdinsight-hadoop-manage-ambari.md)。

也可以在 RxHadoopMR 的调用中使用 Hadoop 开关更改 ML Services 的可用内存量，如下所示：

    hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### <a name="scale-your-cluster"></a>缩放群集

可以通过门户扩展或缩减现有的 HDInsight 上的 ML Services 群集。 通过扩展可以获得更多的容量来完成较大的处理任务；也可以在群集空闲时缩减容量。 有关如何缩放群集的说明，请参阅[管理 HDInsight 群集](../hdinsight-administer-use-portal-linux.md)。

### <a name="maintain-the-system"></a>维护系统

在非工作时间，系统将在 HDInsight 群集的基础 Linux VM 上执行维护，以应用 OS 修补程序和其他更新。 通常，维护操作会在星期一和星期四凌晨 3:30（基于 VM 的本地时间）完成。 执行更新时，每次应该只有不到四分之一的群集会受影响。

由于头节点是冗余的，且并非所有数据节点都受影响，因此在此时间段运行的所有作业可能会变慢。 但是，这些作业应该都可运行完成。 除非发生需要重建群集的灾难性故障，否则任何自定义软件或本地数据在这些维护事件中都将保留。

## <a name="ide-options-for-ml-services-on-hdinsight"></a>适用于 HDInsight 上的 ML Services 的 IDE 选项

HDInsight 群集的 Linux 边缘节点是基于 R 的分析的登录区域。 最新版本的 HDInsight 在边缘节点上提供 RStudio Server 的默认安装，作为基于浏览器的 IDE。 使用 RStudio Server 作为 IDE 来开发和执行 R 脚本，与仅使用 R 控制台相比，可以大幅提高生产力。

此外，可以安装桌面 IDE，并使用它通过远程 MapReduce 或 Spark 计算上下文来访问群集。 选项包括 Microsoft 的[针对 Visual Studio 的 R 工具](https://www.visualstudio.com/features/rtvs-vs.aspx) (RTVS)、RStudio 和 Walware 的基于 Eclipse 的 [StatET](http://www.walware.de/goto/statet)。

此外，通过 SSH 或 PuTTY 连接后，在 Linux 命令提示符下键入 R 即可访问边缘节点上的 R 控制台。 如果在另一个窗口中运行 R 脚本开发的文本编辑器，可根据需要将脚本部分剪切并粘贴到 R 控制台，以便于使用控制台界面。

## <a name="pricing"></a>定价

包含 ML Services 的 HDInsight 群集的相关价格结构与其他 HDInsight 群集类型类似。 这些费用以各种名称、数据和边缘节点的基础 VM 大小为基准，加上核心运行小时数附加费。 有关详细信息，请参阅 [HDInsight 定价](https://azure.microsoft.com/pricing/details/hdinsight/)。

## <a name="next-steps"></a>后续步骤

若要了解有关如何使用 HDInsight 群集上的 ML Services 的详细信息，请参阅以下主题：

* [HDInsight 上的 ML Services 群集入门](r-server-get-started.md)
* [适用于 HDInsight 上的 ML Services 群集的计算上下文选项](r-server-compute-contexts.md)
* [适用于 HDInsight 上的 ML Services 群集的存储选项](r-server-storage.md)