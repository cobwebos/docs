---
title: 机器学习工作室常见问题解答 (FAQ)
titleSuffix: Azure Machine Learning Studio
description: Azure 机器学习工作室：包括用于简化型预测建模的云服务计费、功能和限制的常见问题解答。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 06/02/2017
ms.openlocfilehash: 8c381ca02dcfb194b2807735415ef894d72f74fa
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2019
ms.locfileid: "55462273"
---
# <a name="azure-machine-learning-studio-faq-capabilities-and-limitations"></a>Azure 机器学习工作室常见问题解答：功能和限制
下面提供了一些有关 Azure 机器学习的常见问题 (FAQ) 和相应解答。Azure 机器学习是适合通过 Web 服务开发预测模型和操作解决方案的云服务。 

## <a name="general-questions"></a>一般问题
**什么是机器学习工作室？**

机器学习工作室是一个拖放画布环境，使用 Web 浏览器对其进行访问。 机器学习工作室在可视组合界面中托管了一组模块，有助于以试验形式构建端到端数据科学工作流。

有关机器学习工作室的详细信息，请参阅[什么是机器学习工作室？](what-is-ml-studio.md)

**什么是机器学习 API 服务？**

使用机器学习 API 服务可将预测模型（例如机器学习工作室内置的预测模型）部署为可缩放且容错的 Web 服务。 机器学习 API 服务创建的 Web 服务是 REST API，此类 API 提供的接口可用于外部应用程序与预测分析模型之间的通信。

有关详细信息，请参阅[如何使用 Azure 机器学习 Web 服务](consume-web-services.md)。

**经典 Web 服务列在何处？新 Web 服务（基于 Azure 资源管理器）列在何处？**

使用经典部署模型创建的 Web 服务和使用新 Azure 资源管理器部署模型创建的 Web 服务列在 [Microsoft Azure 机器学习 Web 服务](https://services.azureml.net/)门户中。

此外，还在“Web 服务”选项卡上的[机器学习工作室](http://studio.azureml.net)中列出了经典 Web 服务。

## <a name="azure-machine-learning-questions"></a>Azure 机器学习问题
**什么是 Azure 机器学习 Web 服务？**

机器学习 Web 服务提供了在应用程序和机器学习工作流评分模型之间的接口。 外部应用程序可以使用 Azure 机器学习与机器学习工作流评分模型实时通信。 调用机器学习 Web 服务即可将预测结果返回外部应用程序。 若要调用 Web 服务，需要在传递部署 Web 服务时创建的 API 密钥。 机器学习 Web 服务基于 REST，这是一种流行的web编程项目的体系结构。

Azure 机器学习有两种类型的 Web 服务：

* 请求响应服务 (RRS)：低延迟、高度可缩放的服务，针对使用机器学习工作室创建和部署的无状态模型提供接口。
* 批处理执行服务 (BES)：为一批数据记录进行评分的异步服务。

可通过多种方法来使用 REST API 和访问 Web 服务。 例如，可以使用部署 Web 服务时生成的示例代码，在 C#、R 或 Python 中编写应用程序。

示例代码位于：
- Azure 机器学习 Web 服务门户中的 Web 服务“使用”页面
- 机器学习工作室中 Web 服务仪表板上的 API 帮助页面

也可使用为用户创建的示例 Microsoft Excel 工作簿（在机器学习工作室的 Web 服务仪表板中提供）。

**Azure 机器学习的主要更新有哪些？**

如需最新更新，请参阅 [Azure 机器学习新增功能](../../active-directory/fundamentals/whats-new.md)。

## <a name="import-and-export-data-for-machine-learning"></a>导入和导出机器学习的数据
**机器学习支持哪种数据源？**

可以通过三种方式将数据下载到机器学习工作室试验：

- 上传本地文件作为数据集
- 使用模块从云数据服务导入数据
- 从其他试验导入保存的数据集

要了解有关支持的文件格式的详细信息，请参阅[将训练数据导入机器学习工作室](import-data.md)。

### <a id="ModuleLimit"></a>我的模块的数据集有多大？
机器学习工作室中的模块针对常见用例支持最多包含 10 GB 密集数字数据的数据集。 如果模块接受多个输入，10 GB 这个值是所有输入大小的总计。 也可通过 Hive 或 Azure SQL 数据库查询对更大的数据集采样，或者在引入之前使用“按计数学习”预处理。  

以下数据类型可以在特征规范化期间扩展为较大数据集，其限制为小于 10 GB：

* 稀疏
* 分类
* 字符串
* 二进制数据

以下模块限制为小于 10 GB 的数据集：

* 推荐器模块
* 合成少数类过采样技术 (SMOTE) 模块
* 脚本模块：R、Python、SQL
* 输出数据大小可以大于输入数据大小的模块，例如联接或特征哈希
* 迭代数目极大时的交叉验证、调整模型超参数、顺序回归和一对多的多类

### <a id="UploadLimit"></a>数据上传的限制是什么？
对于大于两个 GB 的数据集，应该将数据上传到 Azure 存储或 Azure SQL 数据库，或者使用 Azure HDInsight，而不要直接从本地文件上传。

**是否可以从 Amazon S3 读取数据？**

如果数据量较小，并且想要通过 HTTP URL 公开，可以使用[导入数据][import-data]模块。 如果数据量较大，请先将它传输到 Azure 存储，然后使用[导入数据][import-data]模块将它导入试验。
<!--

<SEE CLOUD DS PROCESS>
-->

**是否有内置的图像输入功能？**

可以在[导入图像][image-reader]参考中了解图像输入功能。

## <a name="modules"></a>模块
**查找的算法、数据源、数据格式或数据转换操作不在 Azure 机器学习工作室中。可以使用哪些选项？**

可以访问[用户反馈论坛](https://go.microsoft.com/fwlink/?LinkId=404231)，查看我们正在跟踪的功能请求。 如果要找的功能已有人请求，请为该请求投票。 如果要找的功能不存在，请创建新请求。 还可以在此论坛中查看请求状态。 我们将密切跟踪此列表，并经常更新功能可用性状态。 另外，还可以使用对 R 和 Python 的内置支持根据需要创建自定义转换。

**是否可将现有的代码放入机器学习工作室？**

是的，可以在机器学习工作室中放入现有的 R 或 Python 代码，使用 Azure 机器学习的学习器在同一个试验中运行该代码，然后通过 Azure 机器学习将解决方案部署为 Web 服务。 有关详细信息，请参阅[使用 R 扩展试验](extend-your-experiment-with-r.md)和[在 Azure 机器学习工作室中执行 Python 机器学习脚本](execute-python-scripts.md)。

**是否可以使用 [PMML](http://en.wikipedia.org/wiki/Predictive_Model_Markup_Language) 等工具定义模型？**

否，不支持预测模型标记语言 (PMML)。 可以使用自定义 R 和 Python 代码来定义模块。

**在试验中可以并行执行多少个模块？**  

最多可以在一个试验中并行执行四个模块。

## <a name="data-processing"></a>数据处理
**是否可以通过某个功能在试验中以交互方式可视化数据（除 R 可视化以外）？**

单击模块的输出即可将数据可视化并获取统计信息。

**在浏览器中预览结果或数据时，行数和列数有限，为什么？**

这是因为，发送到浏览器的数据量可能很大，限制数据大小是为了防止机器学习工作室速度变慢。 若要可视化所有数据/结果，最好是下载数据并使用 Excel 或其他工具。

## <a name="algorithms"></a>算法
**机器学习工作室支持哪些现有算法？**

机器学习工作室提供最先进的算法，例如可缩放的增强决策树、Bayesian 推荐系统、深度神经网络和决策森林（由 Microsoft Research 开发）。 此外还包括可缩放的开源代码机器学习包，例如 Vowpal Wabbit。 机器学习工作室支持多类与二进制分类、回归和聚集的机器学习算法。 请参阅[机器学习模块][machine-learning-modules]的完整列表。

**是否会自动推荐数据适用的机器学习算法？**

不会，但机器学习工作室有多种方法可以比较每个算法的结果，确定适合解决问题的算法。

**是否提供有关从提供的算法中做出选择的指导？**

请参阅[如何选择算法](algorithm-choice.md)。

**提供的算法是以 R 还是 Python 编写？**

都不是，这些算法主要以编译语言编写，提供更好的性能。

**是否提供任何算法详细信息？**

文档提供了一些有关算法的信息，并描述了可以优化所用算法的微调参数。  

**在线学习是否有任何支持？**

没有，目前仅支持以编程方式进行重新训练。

**是否可以使用内置模块可视化神经网络模型的层？**

不是。

**是否可以使用 C# 或其他语言创建自己的模块？**

目前只能使用 R 创建新的自定义模块。

## <a name="r-module"></a>R 模块
**可以在机器学习工作室中使用哪些 R 包？**

机器学习工作室目前支持 400 多个 CRAN R 包，下面是所有包含的包的[最新列表](http://az754797.vo.msecnd.net/docs/RPackages.xlsx)。 此外，若要了解如何自行检索此列表，请参阅[使用 R 扩展试验](extend-your-experiment-with-r.md)。 如果所需的包不在此列表中，请在[用户反馈论坛](https://go.microsoft.com/fwlink/?LinkId=404231)中提供包名称。

**是否可以构建自定义的 R 模块？**

可以，有关详细信息，请参阅 [Author custom R modules in Azure Machine Learning](custom-r-modules.md) （在 Azure 机器学习中创作自定义 R 模块）。

**是否有适用于 R 的 REPL 环境？**

工作室中没有适用于 R 的 Read-Eval-Print-Loop (REPL) 环境。

## <a name="python-module"></a>Python 模块
**是否可以构建自定义的 Python 模块？**

目前不可以，但可以使用一个或多个[执行 Python 脚本][python]模块来获取相同的结果。

**是否有适用于 Python 的 REPL 环境？**

可以使用机器学习工作室中的 Jupyter 笔记本。 有关详细信息，请参阅 [Introducing Jupyter Notebooks in Azure Machine Learning Studio](http://blogs.technet.com/b/machinelearning/archive/2015/07/24/introducing-jupyter-notebooks-in-azure-ml-studio.aspx)（Azure 机器学习工作室中的 Jupyter 笔记本简介）。

## <a name="web-service"></a>Web 服务

**如何以编程方式重新训练 Azure 机器学习模型？**

使用重新训练 API。 有关详细信息，请参阅[以编程方式重新训练机器学习模型](retrain-models-programmatically.md)。 [Microsoft Azure 机器学习重新训练演示](https://azuremlretrain.codeplex.com/)中也提供了示例代码。

**是否可以在本地或者在没有连接 Internet 的应用程序中部署模型？**

不是。

**所有 Web 服务是否有预期的基准延迟？**

请参阅 [Azure 订阅限制](../../azure-subscription-service-limits.md)。

**以批处理执行服务的形式或者以请求响应服务的形式运行预测模型的时机是什么？**

请求响应服务 (RRS) 是低延迟的大规模 Web 服务，为从试验环境创建并部署的无状态模型提供接口。 批处理执行服务 (BES) 是为一批数据记录进行异步评分的服务。 BES 的输入类似于 RRS 使用的数据输入。 主要区别在于，BES 读取的记录块来自多种源，例如 Azure Blob 存储、Azure 表存储、Azure SQL 数据库、HDInsight（Hive 查询）和 HTTP 源。 有关详细信息，请参阅[如何使用 Azure 机器学习 Web 服务](consume-web-services.md)。

**如何更新已部署的 Web 服务的模型？**

若要为已部署的服务更新预测模型，请修改并重新运行用于编写和保存已训练模型的试验。 提供新版已训练模型后，机器学习工作室将询问是否要更新 Web 服务。 有关如何更新已部署 Web 服务的详细信息，请参阅[部署机器学习 Web 服务](publish-a-machine-learning-web-service.md)。

也可以使用重新训练 API。
有关详细信息，请参阅[以编程方式重新训练机器学习模型](retrain-models-programmatically.md)。 [Microsoft Azure 机器学习重新训练演示](https://azuremlretrain.codeplex.com/)中也提供了示例代码。

**如何监视生产环境中部署的 Web 服务？**

部署预测模型后，可以从 Azure 机器学习 Web 服务门户对其进行监视。 每个已部署的服务都有其本身的仪表板，可以在此处查看该服务的监视信息。 有关如何管理已部署 Web 服务的详细信息，请参阅[使用 Azure 机器学习 Web 服务门户管理 Web 服务](manage-new-webservice.md)和[管理 Azure 机器学习工作区](manage-workspace.md)。

**是否可以在某个位置查看 RRS/BES 输出？**

对于 RRS，通常可以在 Web 服务响应中查看结果。 也可以将结果写入 Azure Blob 存储。 对于BES，输出默认写入 Blob。 也可以使用[导出数据][export-data]模块将输出写入数据库或表。

**是否只能基于机器学习工作室中创建的模型创建 Web 服务？**

不是，也可以直接使用 Jupyter Notebook 和 RStudio 创建 Web 服务。

**哪里可以找到有关错误代码的详细信息？**

有关错误代码与说明的列表，请参阅 [Machine Learning Module Error Codes](https://msdn.microsoft.com/library/azure/dn905910.aspx) （机器学习模块错误代码）。

**什么是 Web 服务的可缩放性？**

目前，每个默认终结点上预配了 20 个并发 RRS 请求。 可以将每个终结点的并发请求调整为 200 个，还可以将每个 Web 服务的终结点调整为 10,000 个，如[缩放 Web 服务](scaling-webservice.md)中所述。 对于 BES，每个终结点每次可处理 40 个请求，超过 40 个的其他请求将排入队列。 这些已排队的请求会在队列清空后自动运行。

**R 作业是否分散在节点之间？**

不是。  

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
* 脚本模块：R、Python、SQL
* 输出数据大小可以大于输入数据大小的模块，例如联接或特征哈希
* 迭代数目极大时的交叉验证、调整模型超参数、顺序回归和一对多的多类

对于大于几 GB 的数据集，可将数据上传到 Azure 存储或 Azure SQL 数据库，或者使用 HDInsight，而不要直接从本地文件上传。

**向量大小是否有任何限制？**

每个行和列的限制为 .NET 的最大 Int 限制：2,147,483,647。

**能否调整运行 Web 服务的虚拟机的大小？**

不是。  

## <a name="security-and-availability"></a>安全性和可用性
**默认情况下，哪些人可以访问 Web 服务的 HTTP 终结点？如何限制对终结点的访问？**

部署 Web 服务之后，将为该服务创建默认终结点。 该默认终结点可以使用其 API 密钥调用。 可以通过 Web Services 门户或编程方式使用 Web 服务管理 API 添加具有自身密钥的更多终结点。 需要使用访问密钥来调用 Web 服务。 有关详细信息，请参阅[如何使用 Azure 机器学习 Web 服务](consume-web-services.md)。

**如果找不到 Azure 存储帐户，会发生什么情况？**

在执行工作流时，机器学习工作室依赖用户提供的 Azure 存储帐户来保存中间数据。 此存储帐户是在创建工作区时提供给机器学习工作室的。 创建工作区之后，如果删除存储帐户且无法再找到它，工作区将停止运行，其中的所有试验会失败。

如果意外删除了存储帐户，可在相同区域重新创建与删除的存储帐户同名的存储帐户。 之后，重新同步访问密钥。

**如果存储帐户访问密钥未同步，会发生什么情况？**

在执行工作流时，机器学习工作室依赖用户提供的 Azure 存储帐户来存储中间数据。 此存储帐户是在创建工作区时提供给机器学习工作室的，访问密钥与该工作区相关联。 如果在创建工作区后更改了访问密钥，该工作区将无法再访问存储帐户。 它将停止工作，并且该工作区中的所有试验都会失败。

如果更改了存储帐户访问密钥，请使用 Azure 门户在工作区中重新同步访问密钥。  


## <a name="billing-questions"></a>计费问题

有关计费和价格信息，请参阅[机器学习定价](https://azure.microsoft.com/pricing/details/machine-learning/)。


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
