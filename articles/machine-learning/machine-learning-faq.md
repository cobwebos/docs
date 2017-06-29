---
title: "Azure 机器学习常见问题 (FAQ) | Microsoft 文档"
description: "Azure 机器学习简介：包括用于简化型预测建模的云服务计费、功能和限制的常见问题。"
keywords: "机器学习简介, 预测建模, 什么是机器学习"
services: machine-learning
documentationcenter: 
author: garyericson
manager: paulettm
editor: cgronlun
ms.assetid: a4a32a06-dbed-4727-a857-c10da774ce66
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/02/2017
ms.author: garye
ms.translationtype: Human Translation
ms.sourcegitcommit: 80be19618bd02895d953f80e5236d1a69d0811af
ms.openlocfilehash: 0a1e23cd52ab5c10791a11d93753b54eb1c1b71a
ms.contentlocale: zh-cn
ms.lasthandoff: 06/07/2017


---
# <a name="azure-machine-learning-frequently-asked-questions-billing-capabilities-limitations-and-support"></a>Azure 机器学习常见问题：计费、功能、限制和支持
下面提供了一些有关 Azure 机器学习的常见问题 (FAQ) 和相应解答。Azure 机器学习是适合通过 Web 服务开发预测模型和操作解决方案的云服务。 这些常见问题涉及到服务使用方法，包括计费模式、功能、限制和支持。

存在此处找不到的问题？

Azure 机器学习在 MSDN 上设有论坛，数据科学社区的成员可以在那里咨询有关 Azure 机器学习的问题。 Azure 机器学习团队负责监管该论坛。 转到 [Azure 机器学习论坛](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning)搜索答案，或者发布自己的新问题。

## <a name="general-questions"></a>一般问题
**什么是 Azure 机器学习？**

Azure 机器学习是完全托管的服务，可用于创建、测试、操作和管理云中的预测分析解决方案。 只需使用浏览器即可登录和上载数据，立即开始机器学习试验。 拖放式预测建模、数量巨大的模块以及包含多种启动模板的库，简化并加速了常见机器学习任务的执行。 有关详细信息，请参阅 [Azure Machine Learning service overview](https://azure.microsoft.com/services/machine-learning/)（Azure 机器学习服务概述）。 如需机器学习简介，了解其中的重要术语和概念，请参阅 [Azure 机器学习简介](machine-learning-what-is-machine-learning.md)。

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

**什么是机器学习工作室？**

机器学习工作室是可通过 Web 浏览器对其进行访问的工作台环境。 机器学习工作室在可视组合界面中托管了一组模块，有助于以试验形式构建端到端数据科学工作流。

有关机器学习工作室的详细信息，请参阅[什么是机器学习工作室？](machine-learning-what-is-ml-studio.md)

**什么是机器学习 API 服务？**

使用机器学习 API 服务可将预测模型（例如机器学习工作室内置的预测模型）部署为可缩放且容错的 Web 服务。 机器学习 API 服务创建的 Web 服务是 REST API，此类 API 提供的接口可用于外部应用程序与预测分析模型之间的通信。

有关详细信息，请参阅[如何使用 Azure 机器学习 Web 服务](machine-learning-consume-web-services.md)。

**经典 Web 服务列在何处？新 Web 服务（基于 Azure Resource Manager）列在何处？**

使用经典部署模型创建的 Web 服务和使用新 Azure Resource Manager 部署模型创建的 Web 服务列在 [Microsoft Azure 机器学习 Web 服务](https://services.azureml.net/)门户中。

此外，还在“Web 服务”选项卡上的[机器学习工作室](http://studio.azureml.net)中列出了经典 Web 服务。

## <a name="azure-machine-learning-questions"></a>Azure 机器学习问题
**什么是 Azure 机器学习 Web 服务？**

机器学习 Web 服务在应用程序和机器学习工作流评分模型之间提供有一个接口。 外部应用程序可以使用 Azure 机器学习与机器学习工作流评分模型实时通信。 调用机器学习 Web 服务即可将预测结果返回外部应用程序。 若要调用 Web 服务，可以传递部署 Web 服务时创建的 API 密钥。 机器学习 Web 服务基于 REST（流行的 Web 编程项目体系结构）。

Azure 机器学习有两种类型的 Web 服务：

* 请求响应服务 (RRS)：低延迟、高度可缩放的服务，针对使用机器学习工作室创建和部署的无状态模型提供接口。
* 批处理执行服务 (BES)：为一批数据记录进行评分的异步服务。

可通过多种方法来使用 REST API 和访问 Web 服务。 例如，可以使用部署 Web 服务时生成的示例代码，在 C#、R 或 Python 中编写应用程序。

示例代码位于：
- Azure 机器学习 Web 服务门户中的 Web 服务“使用”页
- 机器学习工作室中 Web 服务仪表板上的 API 帮助页

也可使用为用户创建的示例 Microsoft Excel 工作簿（在机器学习工作室的 Web 服务仪表板中提供）。

**Azure 机器学习的主要更新有哪些？**

如需最新更新，请参阅 [Azure 机器学习新增功能](machine-learning-whats-new.md)。

## <a name="machine-learning-studio-questions"></a>机器学习工作室问题
### <a name="import-and-export-data-for-machine-learning"></a>导入和导出机器学习的数据
**机器学习支持哪种数据源？**

可以通过三种方式将数据下载到机器学习工作室试验：

- 以数据集形式上载本地文件
- 使用模块从云数据服务导入数据
- 从其他试验导入保存的数据集

若要了解有关支持的文件格式的详细信息，请参阅[将训练数据导入机器学习工作室](machine-learning-data-science-import-data.md)。

#### <a id="ModuleLimit"></a>模块适合多大的数据集？
机器学习工作室中的模块针对常见用例支持最多包含 10 GB 密集数字数据的数据集。 如果模块接受多个输入，10 GB 这个值是所有输入大小的总计。 也可通过 Hive 或 Azure SQL 数据库查询对更大的数据集采样，或者在引入之前使用“按计数学习”预处理。  

以下数据类型可以在特征规范化期间扩展为较大数据集，其限制为小于 10 GB：

* 稀疏
* 分类
* 字符串
* 二进制数据

以下模块限制为小于 10 GB 的数据集：

* 推荐器模块
* 合成少数类过采样技术 (SMOTE) 模块
* 脚本模块：R、Python SQL
* 输出数据大小可以大于输入数据大小的模块，例如联接或特征哈希
* 迭代数目极大时的交叉验证、调整模型超参数、顺序回归和一对多的多类

#### <a id="UploadLimit"></a>数据上载的限制是什么？
对于大于数 GB 的数据集，应该将数据上载到 Azure 存储或 Azure SQL 数据库，或者使用 Azure HDInsight，而不要直接从本地文件上载。

**是否可以从 Amazon S3 读取数据？**

如果数据量较小，并且想要通过 HTTP URL 公开，可以使用[导入数据][import-data]模块。 如果数据量较大，请先将它传输到 Azure 存储，然后使用[导入数据][import-data]模块将它导入试验。
<!--

<SEE CLOUD DS PROCESS>
-->

**是否有内置的图像输入功能？**

可以在[导入图像][image-reader]参考中了解图像输入功能。

### <a name="modules"></a>模块
**查找的算法、数据源、数据格式或数据转换操作不在 Azure 机器学习工作室中。可以使用哪些选项？**

可以访问[用户反馈论坛](http://go.microsoft.com/fwlink/?LinkId=404231)，查看我们跟踪的功能请求。 如果要找的功能已有人请求，请为该请求投票。 如果要找的功能不存在，请创建新请求。 还可以在此论坛中查看请求状态。 我们将密切跟踪此列表，并经常更新功能可用性状态。 另外，还可以使用对 R 和 Python 的内置支持根据需要创建自定义转换。

**是否可将现有的代码放入机器学习工作室？**

是的，可以在机器学习工作室中放入现有的 R 或 Python 代码，使用 Azure 机器学习的学习器在同一个试验中运行该代码，然后通过 Azure 机器学习将解决方案部署为 Web 服务。 有关详细信息，请参阅[使用 R 扩展试验](machine-learning-extend-your-experiment-with-r.md)和[在 Azure 机器学习工作室中执行 Python 机器学习脚本](machine-learning-execute-python-scripts.md)。

**是否可以使用 [PMML](http://en.wikipedia.org/wiki/Predictive_Model_Markup_Language) 等工具定义模型？**

否，不支持预测模型标记语言 (PMML)。 可以使用自定义 R 和 Python 代码来定义模块。

**在试验中可以并行执行多少个模块？**  

最多可以在一个试验中并行执行四个模块。

### <a name="data-processing"></a>数据处理
**是否可以通过某个功能在试验中以交互方式可视化数据（除 R 可视化以外）？**

单击模块的输出即可将数据可视化并获取统计信息。

**在浏览器中预览结果或数据时，行数和列数有限，为什么？**

这是因为，发送到浏览器的数据量可能很大，限制数据大小是为了防止机器学习工作室速度变慢。 若要可视化所有数据/结果，最好是下载数据并使用 Excel 或其他工具。

### <a name="algorithms"></a>算法
**机器学习工作室支持哪些现有算法？**

机器学习工作室提供最先进的算法，例如可缩放的提升决策树、Bayesian 推荐系统、深度神经网络和决策森林（由 Microsoft Research 开发）。 此外还包括可缩放的开源机器学习包，例如 Vowpal Wabbit。 机器学习工作室支持多类与二进制分类、回归和聚集。 请参阅[机器学习模块][machine-learning-modules]的完整列表。

**是否会自动推荐数据适用的机器学习算法？**

不会，但机器学习工作室有多种方法可以比较每个算法的结果，确定适合解决问题的算法。

**是否提供有关从提供的算法中做出选择的指导？**

请参阅[如何选择算法](machine-learning-algorithm-choice.md)。

**提供的算法是以 R 还是 Python 编写？**

都不是，这些算法主要以编译语言编写，提供更好的性能。

**是否提供任何算法详细信息？**

文档提供了一些有关算法的信息，并描述了可以优化所用算法的微调参数。  

**在线学习是否有任何支持？**

没有，目前仅支持以编程方式进行重新训练。

**是否可以使用内置模块可视化神经网络模型的层？**

不会。

**是否可以使用 C# 或其他语言创建自己的模块？**

目前只能使用 R 创建新的自定义模块。

### <a name="r-module"></a>R 模块
**可以在机器学习工作室中使用哪些 R 包？**

机器学习工作室目前支持 400 多个 CRAN R 包，下面是所有包含的包的[最新列表](http://az754797.vo.msecnd.net/docs/RPackages.xlsx)。 此外，若要了解如何自行检索此列表，请参阅[使用 R 扩展试验](machine-learning-extend-your-experiment-with-r.md)。 如果所需的包不在此列表中，请在[用户反馈论坛](http://go.microsoft.com/fwlink/?LinkId=404231)中提供包名称。

**是否可以构建自定义的 R 模块？**

可以，有关详细信息，请参阅 [Author custom R modules in Azure Machine Learning](machine-learning-custom-r-modules.md) （在 Azure 机器学习中创作自定义 R 模块）。

**是否有适用于 R 的 REPL 环境？**

工作室中没有适用于 R 的 Read-Eval-Print-Loop (REPL) 环境。

### <a name="python-module"></a>Python 模块
**是否可以构建自定义的 Python 模块？**

目前不可以，但可以使用一个或多个[执行 Python 脚本][python]模块来获取相同的结果。

**是否有适用于 Python 的 REPL 环境？**

可以使用机器学习工作室中的 Jupyter 笔记本。 有关详细信息，请参阅 [Introducing Jupyter Notebooks in Azure Machine Learning Studio](http://blogs.technet.com/b/machinelearning/archive/2015/07/24/introducing-jupyter-notebooks-in-azure-ml-studio.aspx)（Azure 机器学习工作室中的 Jupyter 笔记本简介）。

## <a name="web-service"></a>Web 服务
### <a name="retrain"></a>重新训练
**如何以编程方式重新训练 Azure 机器学习模型？**

使用重新训练 API。 有关详细信息，请参阅[以编程方式重新训练机器学习模型](machine-learning-retrain-models-programmatically.md)。 [Microsoft Azure 机器学习重新训练演示](https://azuremlretrain.codeplex.com/)中也提供了示例代码。

### <a name="create"></a>创建
**是否可以在本地或者在没有连接 Internet 的应用程序中部署模型？**

不会。

**所有 Web 服务是否有预期的基准延迟？**

请参阅 [Azure 订阅限制](../azure-subscription-service-limits.md)。

### <a name="use"></a>使用
**以批处理执行服务的形式或者以请求响应服务的形式运行预测模型的时机是什么？**

请求响应服务 (RRS) 是低延迟的大规模 Web 服务，为从试验环境创建并部署的无状态模型提供接口。 批处理执行服务 (BES) 是为一批数据记录进行异步评分的服务。 BES 的输入类似于 RRS 使用的数据输入。 主要区别在于，BES 读取的记录块来自多种源，例如 Azure Blob 存储、Azure 表存储、Azure SQL 数据库、HDInsight（Hive 查询）和 HTTP 源。 有关详细信息，请参阅[如何使用 Azure 机器学习 Web 服务](machine-learning-consume-web-services.md)。

**如何更新已部署的 Web 服务的模型？**

若要为已部署的服务更新预测模型，请修改并重新运行用于编写和保存已训练模型的试验。 提供新版已训练模型后，机器学习工作室将询问是否要更新 Web 服务。 有关如何更新已部署 Web 服务的详细信息，请参阅[部署机器学习 Web 服务](machine-learning-publish-a-machine-learning-web-service.md)。

也可以使用重新训练 API。
有关详细信息，请参阅[以编程方式重新训练机器学习模型](machine-learning-retrain-models-programmatically.md)。 [Microsoft Azure 机器学习重新训练演示](https://azuremlretrain.codeplex.com/)中也提供了示例代码。

**如何监视生产环境中部署的 Web 服务？**

部署预测模型后，可以从 Azure 经典门户（仅经典 Web 服务）或 Azure 机器学习 Web 服务门户对其进行监视。 每个已部署的服务都有其本身的仪表板，可以在此处查看该服务的监视信息。 有关如何管理已部署 Web 服务的详细信息，请参阅[使用 Azure 机器学习 Web 服务门户管理 Web 服务](machine-learning-manage-new-webservice.md)和[管理 Azure 机器学习工作区](machine-learning-manage-workspace.md)。

**是否可以在某个位置查看 RRS/BES 输出？**

对于 RRS，通常可以在 Web 服务响应中查看结果。 也可以将结果写入 Azure Blob 存储。 对于BES，输出默认写入 Blob。 也可以使用[导出数据][export-data]模块将输出写入数据库或表。

**是否只能基于机器学习工作室中创建的模型创建 Web 服务？**

不是，也可以直接使用 Jupyter Notebook 和 RStudio 创建 Web 服务。

**哪里可以找到有关错误代码的详细信息？**

有关错误代码与说明的列表，请参阅 [Machine Learning Module Error Codes](https://msdn.microsoft.com/library/azure/dn905910.aspx) （机器学习模块错误代码）。

## <a name="scalability"></a>可伸缩性
**什么是 Web 服务的可缩放性？**

目前，每个默认终结点上预配了 20 个并发 RRS 请求。 可以将每个终结点的并发请求调整为 200 个，还可以将每个 Web 服务的终结点调整为 10,000 个，如[缩放 Web 服务](machine-learning-scaling-webservice.md)中所述。 对于 BES，每个终结点每次可处理 40 个请求，超过 40 个的其他请求将排入队列。 这些已排队的请求会在队列清空后自动运行。

**R 作业是否分散在节点之间？**

没有。  

**可以将多少数据用于训练？**

机器学习工作室中的模块针对常见用例支持最多包含 10 GB 密集数字数据的数据集。 如果模块接受多个输入，所有输入的大小总计为 10 GB。 也可以通过 Hive 查询、Azure SQL 数据库查询或在引入之前使用[按计数学习][counts]模块进行的预处理对更大的数据集采样。  

以下数据类型可以在特征规范化期间扩展为较大数据集，其限制为小于 10 GB：

* 稀疏
* 分类
* 字符串
* 二进制数据

以下模块限制为小于 10 GB 的数据集：

* 推荐器模块
* 合成少数类过采样技术 (SMOTE) 模块
* 脚本模块：R、Python SQL
* 输出数据大小可以大于输入数据大小的模块，例如联接或特征哈希
* 迭代数目极大时的交叉验证、调整模型超参数、顺序回归和一对多的多类

对于大于几 GB 的数据集，可将数据上载到 Azure 存储或 Azure SQL 数据库，或者使用 HDInsight，而不要直接从本地文件上载。

**向量大小是否有任何限制？**

每个行和列的限制为 .NET 的最大 Int 限制：2,147,483,647。

**能否调整运行 Web 服务的虚拟机的大小？**

不会。  

## <a name="security-and-availability"></a>安全性和可用性
**默认情况下，哪些人可以访问 Web 服务的 HTTP 终结点？如何限制对终结点的访问？**

部署 Web 服务之后，将为该服务创建默认终结点。 该默认终结点可以使用其 API 密钥调用。 可以从 Azure 经典门户，或以编程方式使用 Web 服务管理 API 添加具有自身密钥的更多终结点。 需要使用访问密钥来调用 Web 服务。 有关详细信息，请参阅[如何使用 Azure 机器学习 Web 服务](machine-learning-consume-web-services.md)。

**如果找不到 Azure 存储帐户，会发生什么情况？**

在执行工作流时，机器学习工作室依赖用户提供的 Azure 存储帐户来保存中间数据。 此存储帐户是在创建工作区时提供给机器学习工作室的。 创建工作区之后，如果删除存储帐户且无法再找到它，工作区将停止运行，其中的所有试验将会失败。

如果意外删除了存储帐户，可在相同区域重新创建与删除的存储帐户同名的存储帐户。 然后，重新同步访问密钥。

**如果存储帐户访问密钥未同步，会发生什么情况？**

在执行工作流时，机器学习工作室依赖用户提供的 Azure 存储帐户来存储中间数据。 此存储帐户是在创建工作区时提供给机器学习工作室的，访问密钥与该工作区相关联。 如果在创建工作区后更改了访问密钥，该工作区将无法再访问存储帐户。 它将停止工作，并且该工作区中的所有试验都将失败。

如果更改了存储帐户访问密钥，请使用 Azure 经典门户在工作区中重新同步访问密钥。  

## <a name="support-and-training"></a>支持和培训
**在哪里可以获得 Azure 机器学习的培训？**

[Azure 机器学习文档中心](https://azure.microsoft.com/services/machine-learning/)提供视频教程和操作指南。 这些分步指南提供服务简介，并对使用 Azure 机器学习导入数据、清除数据、构建预测模型以及将其部署到生产环境中等整个数据科研生命周期进行了说明。

我们会持续更新机器学习中心的内容。 可以在 [用户反馈论坛](https://windowsazure.uservoice.com/forums/257792-machine-learning)中提交有关机器学习中心的其他学习材料请求。

也可以在 [Microsoft 虚拟大学](http://www.microsoftvirtualacademy.com/training-courses/getting-started-with-microsoft-azure-machine-learning)查找培训教程。

**如何获取 Azure 机器学习的支持？**

若要获取 Azure 机器学习的技术支持，请转到 [Azure 支持](/support/options/)并选择“机器学习”。

Azure 机器学习还在 MSDN 上设有社区论坛，可以在那里咨询有关 Azure 机器学习的问题。 Azure 机器学习团队负责监管该论坛。 访问 [Azure 论坛](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning)。

## <a name="billing-questions"></a>计费问题
**机器学习的计费模式是怎样的？**

Azure 机器学习有两个组件：机器学习工作室和机器学习 Web 服务。

评估机器学习工作室时，可以使用免费计费层。 使用免费层也可以部署容量有限的经典 Web 服务。

如果认定 Azure 机器学习符合需要，则可以注册标准层。 若要注册，必须有一个 Microsoft Azure 订阅。

在标准层中，需要按月对每个在机器学习工作室中定义的工作区付费。 在工作室中运行试验时，需要根据运行试验时的计算资源使用量付费。 部署经典 Web 服务时，将会根据即用即付模式计收事务和计算小时数费用。

新的（基于 Resource Manager）Web 服务引入了可提高成本可预测性的计费计划。 分层定价向需要大容量的客户提供折扣费率。

创建计划即表示遵守固定费用，换取 API 计算小时数和 API 事务数的已包含数量。 如果需要更多的已包含数量，可以在计划中添加实例。 如果需要大量的已包含数量，可以选择更高层的计划，其中提供大量的已包含数量和更优惠的折扣价。

在现有实例的已包含数量用完之后，将根据与计费计划层关联的超额费率对额外使用量收费。

> [!NOTE]
已包含数量每 30 天重新分配一次，未使用的已包含数量不会滚存到下一期。

有关其他计费和价格信息，请参阅 [Machine Learning Pricing](https://azure.microsoft.com/pricing/details/machine-learning/)（机器学习定价）。

**机器学习是否有免费试用版？**

 Azure 机器学习有一个免费订阅选项，详见[机器学习定价](https://azure.microsoft.com/pricing/details/machine-learning/)。 机器学习工作室有一个八小时快速评估试用版，用户登录到[机器学习工作室](https://studio.azureml.net/?selectAccess=true&o=2)即可使用该版本。

 此外，注册 Azure 免费试用版后，可以试用任何 Azure 服务一个月。 若要详细了解 Azure 免费试用版，请访问 [Azure Free Trial FAQ](https://azure.microsoft.com/pricing/free-trial-faq/)（Azure 免费试用常见问题）。

**什么是事务？**

事务代表 Azure 机器学习响应的 API 调用。 来自请求-响应服务 (RRS) 和批处理执行服务 (BES) 调用的事务将会聚合，根据计费计划收费。

**计划中附送的事务数量是否可用于 RRS 和 BES 事务？**

可以，来自 RRS 和 BES 的事务将会聚合，根据计费计划收费。

**什么是 API 计算小时数？**

API 计算小时数是使用机器学习计算资源运行 API 调用所花费时间的计费单位。 所有调用将会聚合以便计费。

**生产型 API 调用通常需要多长时间？**

生产型 API 调用时间可能差异很大，通常为数百毫秒到数秒。 某些 API 调用可能需要数分钟，具体取决于数据处理和机器学习模型的复杂性。 估算生产型 API 调用时间的最佳方法是在机器学习服务中指定一个基准模型。

**什么是工作室计算小时数？**

工作室计算小时数是试验在工作室中使用计算资源的聚合时间计费单位。

**在新（基于 Azure Resource Manager）Web 服务中，开发/测试层的用途是什么？**

基于 Resource Manager 的 Web 服务提供多个可用于预配计费计划的层。 开发/测试定价层提供有限的已包含数量，让用户以 Web 服务的形式测试试验，而不产生费用。 用户可以查看其运行情形。

**是否需要单独支付存储费用？**

机器学习免费层不需要或禁止单独存储。 机器学习标准层要求用户具有 Azure 存储帐户。 Azure 存储[单独计费](https://azure.microsoft.com/pricing/details/storage/)。

**机器学习是否支持高可用性？**

是的。 有关详细信息，请参阅[机器学习定价](https://azure.microsoft.com/en-us/pricing/details/machine-learning/)，了解服务级别协议 (SLA) 的说明。

**具体而言，将在哪种计算资源上运行生产 API 调用？**

机器学习服务是多租户服务。 在后端使用的实际计算资源存在差异，并且针对性能和可预测性进行了优化。

### <a name="management-of-new-resource-manager-based-web-services"></a>管理新（基于 Resource Manager）Web 服务
**如果删除计划，会发生什么情况？**

该计划将从订阅中删除，并按比例计算使用费。

> [!NOTE]
无法删除 Web 服务正在使用的计划。 若要删除该计划，必须向 Web 服务分配新计划，或删除 Web 服务。

**什么是计划实例？**

计划实例是可在计费计划中添加的已包含数量单位。 为计费计划选择计费层时，计划会随附一个实例。 如果需要更多的已包含数量，可以在计划中添加所选计费层的实例。

**可以添加多少个计划实例？**

在订阅中可以有一个开发/测试定价层实例。

对于标准 S1、标准 S2 和标准 S3 层，可以根据需要添加相应数目的层。

> [!NOTE]
根据预期使用量，更符合成本效益的方式可能是升级到已包含数量更多的层，而不是在当前层中添加实例。

**更改计划层（升级/降级）会发生什么情况？**

旧计划将被删除，当前使用量将按比例计费。 针对剩余期限创建新计划，其中将有升级/降级层的完整已包含数量。

> [!NOTE]
已包含数量按期限分配，未使用的数量不会滚存。

**在计划中增加实例会发生什么情况？**

按比例包含数量，可能需要 24 小时才能生效。

**删除计划实例会发生什么情况？**

该实例将从订阅中删除，并按比例计算使用费。

### <a name="sign-up-for-new-resource-manager-based-web-services-plans"></a>注册新（基于 Resource Manager）Web 服务计划
**如何注册计划？**

可以使用两种方法创建计费计划。

首次部署基于 Resource Manager 的 Web 服务时，可以选择现有计划或创建新计划。

以这种方式创建的计划位于默认区域中，Web 服务将部署到该区域。

如果要将服务部署到非默认区域，可能需要在部署服务之前定义计费计划。

在此情况下，可以登录 Azure 机器学习 Web 服务门户，然后转到“计划”页。 可以在该处添加和删除计划，以及修改现有计划。

**应该从哪个计划开始？**

建议从标准 S1 层开始，并监视服务的使用量。 如果发现已包含数量用得很快，可以添加实例，或者改用更高的计划层并获得更优惠的折扣价。 在整个计费周期内，可以根据需要调整计费计划。

**哪些区域提供新计划？**

支持新 Web 服务的三个生产区域提供新的计费计划：

* 美国中南部
* 欧洲西部
* 东南亚

**如果在多个区域拥有 Web 服务，是否每个区域都需要一个计划？**

是的。 不同的区域有不同的计划定价。 将 Web 服务部署到其他区域时，需要为服务分配该区域特定的计划。 有关详细信息，请参阅 [可用产品（按区域）]( https://azure.microsoft.com/regions/services/)。

### <a name="new-web-services-overages"></a>新的 Web 服务：超额
**如何检查 Web 服务使用量是否超额？**

可以在 Azure 机器学习 Web 服务门户的“计划”页中查看所有计划的使用量。 登录到门户，然后单击“计划”菜单选项。

在表的“事务”和“计算”列中，可以看到计划的已包含数量和已用数量百分比。

**开发/测试定价层中的已包含数量用完时会发生什么情况？**

分配有开发/测试定价层的服务将会停止，直到下一个周期来临，或者直到将它们改到付费层为止。

**对于经典 Web 服务和超额的新（基于 Resource Manager）Web 服务，如何计算请求响应 (RRS) 和批处理 (BES) 工作负荷的价格？**

对于 RRS 工作负荷，需要对进行的每次 API 事务调用以及与这些请求关联的计算时间付费。 RRS 生产 API 事务成本以所进行的 API 调用总数乘以每 1,000 个事务的价格（单个事务按比例计费）得出。 RRS API 生产 API 计算小时数成本以运行每个 API 调用所需的时间量乘以 API 事务总数，再乘以每个生产 API 计算小时数的价格得出。

例如，对于标准 S1 超额而言，有 1,000,000 个 API 事务，且每个事务的运行时间为 0.72 秒，生产 API 事务成本为 $500（1,000,000 * $0.50/1000 个 API 事务），生产 API 计算小时数成本为 $400（1,000,000 * 0.72 秒 * $2/小时），总计为 $900。

BES 工作负荷的计费方式相同， 但 API 事务成本表示提交的批处理作业数，计算成本表示与这些批处理作业关联的计算时间。 BES 生产 API 事务成本以提交的作业总数乘以每 1,000 个事务的价格（单个事务按比例计费）得出。 BES API 生产 API 计算小时数成本以运行作业中每行所需时间量乘以作业中的总行数、作业总数和每个生产 API 计算小时数的价格得出。 使用机器学习计算器时，事务指示器表示计划提交的作业数，而“每个事务的时间”字段表示运行每个作业中的所有行所需的总时间。

例如，假定标准 S1 超额，而你每天提交 100 个作业，每个作业包含 500 行，每行需时 0.72 秒。 每月生产 API 事务成本为 $1.55（100 个作业/天 = 3,100 个作业/月 * $0.50/1000 个 API 事务），生产 API 计算小时数成本为 $620（500 行 * 0.72 秒 * 3100 个作业 * $2/小时），总计超额成本为 $621.55。

### <a name="azure-machine-learning-classic-web-services"></a>Azure 机器学习经典 Web 服务
**是否还提供即用即付模式？**

是，Azure 机器学习仍提供经典 Web 服务。  

### <a name="azure-machine-learning-free-and-standard-tier"></a>Azure 机器学习免费层和标准层
**Azure 机器学习免费层中包括什么？**

Azure 机器学习免费层旨在提供针对 Azure 机器学习工作室的深入介绍。 只需使用 Microsoft 帐户即可注册。 免费层包括每个 [Microsoft 帐户](https://www.microsoft.com/account/default.aspx)免费访问一个 Azure 机器学习工作室工作区。 在此层中，可以使用最多 10 GB 存储，并将模型作为过渡 API 进行操作。 免费层工作负载不在 SLA 范围内，只适合开发和个人使用。 

免费层工作区存在以下限制：

* 工作负荷无法通过连接本地服务器（用于运行 SQL Server）来访问数据。
* 无法部署新的 Resource Manager 库 Web 服务。


**Azure 机器学习标准层和计划中包括什么？**

Azure 机器学习标准层是 Azure 机器学习工作室的付费版。 Azure 机器学习工作室的月费每月按工作区计算，不足一个月的按天数计算。 Azure 机器学习工作室试验小时数按有效试验的计算小时数计费。 不足一小时的在折算成小时数后计费。  

Azure 机器学习 API 服务根据它是经典 Web 服务还是新（基于 Resource Manager）Web 服务来计费。

以下费用针对订阅的每个工作区进行累计。

* 机器学习工作区订阅：机器学习工作区订阅按月计费，用于访问机器学习工作室工作区。 必须使用该订阅才能在工作室中运行试验以及利用生产 API。
* 工作室试验小时数：此指标聚合了在机器学习工作室中运行试验和在过渡环境中运行生产型 API 调用所产生的所有计算费用。
* 通过连接本地服务器来访问数据，该服务器在训练和评分模型中运行 SQL Server。
* 对于经典 Web 服务：
  * 生产 API 计算小时数：此指标包括在生产中运行的 Web 服务所产生的计算费用。
  * 生产 API 事务数（以 1000 次为单位）：此指标包括每次调用生产 Web 服务所产生的费用。

除前述费用外，如果是基于 Resource Manager 的 Web 服务，费用将聚合到选定计划：

* 标准 S1/S2/S3 API 计划（单位）：此指标表示为基于 Resource Manager 的 Web 服务选择的实例类型。
* 标准 S1/S2/S3 超额 API 计算小时数：此指标包括现有实例中包含的数量用完后，基于 Resource Manager 的 Web 服务在生产环境中运行所产生的计算费用。 额外的使用量将按照与 S1/S2/S3 计划层关联的超额费率收费。
* 标准 S1/S2/S3 超额 API 事务（以 1,000 计）：此指标包括现有实例中包含的数量用完后，对生产环境的基于 Resource Manager 的 Web 服务进行的每个调用所产生的费用。 额外的使用量将按照与 S1/S2/S3 计划层关联的超额费率收费。
* 已包含的 API 计算小时数：在基于 Resource Manager 的 Web 服务中，此指标表示 API 计算小时数的已包含数量。
* 已包含的 API 事务数（以 1,000 计）：在基于 Resource Manager 的 Web 服务中，此指标表示 API 事务的已包含数量。

**如何注册 Azure 机器学习免费层？**

只需拥有一个 Microsoft 帐户即可。 转到 [Azure 机器学习主页](https://azure.microsoft.com/services/machine-learning/)，然后单击“立即开始”。 使用 Microsoft 帐户登录，系统将为你在免费层中创建一个工作区。 然后可以立即开始浏览和创建机器学习试验。

**如何注册 Azure 机器学习标准层？**

必须首先具有对 Azure 订阅的访问权限，然后才能创建标准机器学习工作区。 可以注册使用为期 30 天的 Azure 订阅免费试用，试用结束后再升级到 Azure 付费订阅，也可以立即购买 Azure 付费订阅。 然后，可以在获取订阅访问权限后从 Microsoft Azure 经典门户创建机器学习工作区。 请查看[分步说明](https://azure.microsoft.com/trial/get-started-machine-learning-b/)。

或者，可以接受标准机器学习工作区所有者的邀请访问其工作区。

**是否可以指定自己的用于免费层的 Azure Blob 存储帐户？**

不可以，标准层等同于在引入层前可用的机器学习服务版本。

**是否可以在免费层中将机器学习模型部署为 API？**

是的，可以操作机器学习模型，使其成为免费层的一部分，即过渡 API 服务。 为了将过渡 API 服务投入生产并获取可操作性服务的生产终结点，必须使用标准层。

**Azure 免费试用版与 Azure 机器学习免费层之间有何区别？**

[Microsoft Azure 免费试用版](https://azure.microsoft.com/free/)提供一个月可应用于任何 Azure 服务的信用额度。 具体而言，Azure 机器学习免费层提供针对非生产工作负荷的 Azure 机器学习的连续访问。

**如何将实验从免费层移到标准层？**

将试验从免费层复制到标准层：

1. 登录到 Azure 机器学习工作室，确保免费工作区和标准工作区均在顶部导航栏的工作区选择器中可见。
2. 如果当前处于标准工作区，请切换到免费工作区。
3. 在试验列表视图中，选择要复制的试验，然后单击“复制”命令按钮。
4. 在打开的对话框中选择标准工作区，然后单击“复制”按钮。
   所有关联数据集、定型模型等都会随试验一起复制到标准工作区。
5. 需要在标准工作区重新运行试验并重新发布 Web 服务。

### <a name="studio-workspace"></a>工作室工作区
**使用不同的工作区是否会产生不同的帐单？**

一份帐单上，每笔工作区费用会根据每个适用的指标单独列出。

**要在哪种特定类型的计算资源上运行试验？**

机器学习服务是多租户服务。 在后端使用的实际计算资源存在差异，并且针对性能和可预测性进行了优化。

### <a name="guest-access"></a>来宾访问
**什么是对 Azure 机器学习工作室进行来宾访问？**

来宾访问是受限的试用体验。 用户可以在 Azure 机器学习工作室中创建和运行试验，不需任何费用，也不需身份验证。 来宾会话是非永久性的（不可保存），并且时间限制为八小时。 其他限制包括缺少对 R 和 Python 的支持、不存在过渡 API，以及数据集大小和存储容量受限。 相比之下，选择使用 Microsoft 帐户登录的用户具有对上文描述的机器学习工作室免费层的完全访问权限，包括永久工作区和更完整的功能。 若要选择免费机器学习体验，可单击 [https://studio.azureml.net](https://studio.azureml.net) 上的“入门”，然后选择“来宾访问”或使用 Microsoft 帐户登录。

<!-- Module References -->
[image-reader]: https://msdn.microsoft.com/library/azure/893f8c57-1d36-456d-a47b-d29ae67f5d84/
[join]: https://msdn.microsoft.com/library/azure/124865f7-e901-4656-adac-f4cb08248099/
[machine-learning-modules]: https://msdn.microsoft.com/library/azure/6d9e2516-1343-4859-a3dc-9673ccec9edc/
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[export-data]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[python]: https://msdn.microsoft.com/library/azure/CDB56F95-7F4C-404D-BDE7-5BB972E6F232
[counts]: https://msdn.microsoft.com/library/azure/dn913056.aspx

