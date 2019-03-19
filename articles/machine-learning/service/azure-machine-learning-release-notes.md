---
title: 版本新增内容
titleSuffix: Azure Machine Learning service
description: 了解 Azure 机器学习服务的最新更新以及机器学习和数据准备 Python SDK。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: larryfr
author: Blackmist
ms.date: 03/11/2019
ms.custom: seodec18
ms.openlocfilehash: 2a2817501628d55d7ccc84979700ea53e4114eed
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "57860629"
---
# <a name="azure-machine-learning-service-release-notes"></a>Azure 机器学习服务发行说明

本文介绍 Azure 机器学习服务版本。  有关每个 SDK 的完整说明，请访问以下内容的参考文档：
+ Azure 机器学习[适用于 Python 的主 SDK](https://aka.ms/aml-sdk)
+ Azure 机器学习[数据准备 SDK](https://aka.ms/data-prep-sdk)

## <a name="2019-03-11"></a>2019-03-11

### <a name="azure-machine-learning-sdk-for-python-v1018"></a>Azure Machine Learning SDK for Python v1.0.18

 + **更改**
   + Azureml tensorboard 程序包将取代 azureml contrib tensorboard。
   + 此版本中，可以创建它时，用户帐户上托管的计算群集 (amlcompute) 设置。 这可以通过只需将这些属性传递设置配置中。 您可以找到更多详细信息中的[SDK 参考文档](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none-)。

### <a name="azure-machine-learning-data-prep-sdk-v1017"></a>Azure 机器学习数据准备 SDK v1.0.17

+ **新功能**
  + 现在支持添加两个数值列来生成结果的列使用表达式语言。

+ **Bug 修复和改进**
  + 改进的文档和参数检查 random_split。
  
## <a name="2019-02-27"></a>2019-02-27

### <a name="azure-machine-learning-data-prep-sdk-v1016"></a>Azure 机器学习数据准备 SDK v1.0.16

+ **Bug 修复**
  + 修复了导致的身份验证问题的服务主体的 API 更改。

## <a name="2019-02-25"></a>2019-02-25

### <a name="azure-machine-learning-sdk-for-python-v1017"></a>Azure Machine Learning SDK for Python v1.0.17

+ **新功能**

  + Azure 机器学习现在为常用的 DNN 框架，链接器提供一流的支持。 使用[ `Chainer` ](https://docs.microsoft.com/en-us/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py)类用户可以轻松地训练和部署链接器模型。
    + 了解如何[使用 ChainerMN 运行分布式的培训](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/distributed-chainer/distributed-chainer.ipynb)
    + 了解如何[运行链接器使用 HyperDrive 与超参数优化](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb)
  + Azure 机器学习管道添加管道运行基于数据存储的修改可以触发。 管道[计划 notebook](https://aka.ms/pl-schedule)会更新，以展示此功能。

+ **Bug 修复和改进**
  + 我们添加了支持 Azure 机器学习管道为 source_directory_data_store 属性设置为的所需的数据存储 （如 blob 存储） 上[RunConfigurations](https://docs.microsoft.com/en-us/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py)提供给[PythonScriptStep](https://docs.microsoft.com/en-us/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py)。 默认情况下步骤作为后备数据存储，可能会遇到限制问题，同时执行大量的步骤时使用 Azure 文件存储。

### <a name="azure-portal"></a>Azure 门户

+ **新功能**
  + 新的拖放功能表报表编辑器的体验。 用户可以将列从井中到表的预览将显示其中的表区域。 可以重新排列列。
  + 新的日志文件查看器
  + 尝试运行、 计算、 模型、 图像和从活动选项卡的部署的链接

### <a name="azure-machine-learning-data-prep-sdk-v1015"></a>Azure 机器学习数据准备 SDK v1.0.15

+ **新功能**
  + 数据准备现在支持从数据流写入文件流。 此外提供的功能来处理要创建新的文件名称的文件流名称。
    + 操作方法指南：[使用与文件流 notebook](https://aka.ms/aml-data-prep-file-stream-nb)

+ **Bug 修复和改进**
  + 改进了的 t-Digest 对大型数据集的性能。
  + 数据准备现在支持从数据路径的读取数据。
  + 一种热编码现在适用于布尔值和数字列。
  + 其他杂项的 bug 修复。

## <a name="2019-02-11"></a>2019-02-11

### <a name="azure-machine-learning-sdk-for-python-v1015"></a>适用于 Python 的 Azure 机器学习 SDK v1.0.15

+ **新功能**
  + Azure 机器学习管道增加了 AzureBatchStep（[笔记本](https://aka.ms/pl-azbatch)）、HyperDriveStep（[笔记本](https://aka.ms/pl-hyperdrive)）和基于时间的计划功能（[笔记本](https://aka.ms/pl-schedule)）。
  +  DataTranferStep 已更新，可以与 Azure SQL Server 和 Azure database for PostgreSQL ([notebook](https://aka.ms/pl-data-trans)) 配合使用。

+ **更改**
  + 弃用了 `PublishedPipeline.get_published_pipeline` 而改用 `PublishedPipeline.get`。
  + 弃用了 `Schedule.get_schedule` 而改用 `Schedule.get`。

### <a name="azure-machine-learning-data-prep-sdk-v1012"></a>Azure 机器学习数据准备 SDK v1.0.12

+ **新功能**
  + 数据准备现在支持使用数据存储从 Azure SQL 数据库读取数据。
 
+ **更改**
  + 显著改进了对大型数据的某些操作的内存性能。
  + `read_pandas_dataframe()` 现在要求指定 `temp_folder`。
  + `ColumnProfile` 的 `name` 属性已弃用 - 请改用 `column_name`。

## <a name="2019-01-28"></a>2019-01-28

### <a name="azure-machine-learning-sdk-for-python-v1010"></a>适用于 Python 的 Azure 机器学习 SDK v1.0.10

+ **更改**： 
  + Azure 机器学习 SDK 不再将 azure-cli 包作为依赖项。 具体而言，azure-cli-core 和 azure-cli-profile 已从 azureml-core 中删除。 以下是对用户有所影响的更改：
    + 如果在执行“az login”，然后使用 azureml-sdk，则 SDK 会再次执行浏览器或设备代码登录。 它不会使用“az login”所创建的任何凭据状态。
    + 对于 Azure CLI 身份验证（如使用“az login”），请使用 azureml.core.authentication.AzureCliAuthentication 类。 对于 Azure CLI 身份验证，请在安装了 azureml-sdk 的 Python 环境中执行 pip install azure-cli。
    + 如果在使用服务主体执行“az login”以实现自动化，则建议使用 azureml.core.authentication.ServicePrincipalAuthentication 类，因为 azureml-sdk 不会使用 azure CLI 所创建的凭据状态。 

+ **Bug 修复**：此版本主要包含次要 Bug 修复

### <a name="azure-machine-learning-data-prep-sdk-v108"></a>Azure 机器学习数据准备 SDK v1.0.8

+ **Bug 修复**
  + 显著改进了获取数据配置文件的性能。
  + 修复了与错误报告相关的次要 bug。
  
### <a name="azure-portal-new-features"></a>Azure 门户：新功能
+ 报表的新拖放图表体验。 用户可以将列或属性从井中拖动到图表区域，系统会在其中基于数据类型自动选择适合于用户的图表类型。 用户可以将图表类型更改为其他适用类型或添加其他属性。

    支持的图表类型：
    - 折线图
    - 直方图
    - 堆积条形图
    - 盒须图
    - 散点图
    - 气泡图
+ 门户现在为试验动态生成报表。 当用户提交试验运行时，报表将自动生成，其中包含记录的指标和图形，以便跨不同运行进行比较。 

## <a name="2019-01-14"></a>2019-01-14

### <a name="azure-machine-learning-sdk-for-python-v108"></a>适用于 Python 的 Azure 机器学习 SDK v1.0.8

+ **Bug 修复**：此版本主要包含次要 Bug 修复

### <a name="azure-machine-learning-data-prep-sdk-v107"></a>Azure 机器学习数据准备 SDK v1.0.7

+ **新功能**
  + 数据存储改进（记录在[数据存储区操作指南](https://aka.ms/aml-data-prep-datastore-nb)中）
    + 添加了在扩展中读取和写入 Azure 文件共享和 ADLS 数据存储的功能。
    + 使用数据存储时，数据准备现在支持使用服务主体身份验证而不是交互式身份验证。
    + 添加了的对 wasb 和 wasbs url 的支持。

## <a name="2019-01-09"></a>2019-01-09

### <a name="azure-machine-learning-data-prep-sdk-v106"></a>Azure 机器学习数据准备 SDK v1.0.6

+ **Bug 修复**
  + 修复了从 Spark 上的公共可读 Azure Blob 容器读取时出现的 bug

## <a name="2018-12-20"></a>2018-12-20 

### <a name="azure-machine-learning-sdk-for-python-v106"></a>适用于 Python 的 Azure 机器学习 SDK v1.0.6
+ **Bug 修复**：此版本主要包含次要 Bug 修复

### <a name="azure-machine-learning-data-prep-sdk-v104"></a>Azure 机器学习数据准备 SDK v1.0.4

+ **新功能**
  + 现在可以通过 `to_bool` 函数将不匹配的值转换为“错误”值。 这是针对 `to_bool` 和 `set_column_types` 的新的默认不匹配行为，而旧的默认行为是将不匹配的值转换为 False。
  + 调用 `to_pandas_dataframe` 时，可以通过新的选项将数字列中的 null/缺失值解释为 NaN。
  + 增加了检查某些表达式的返回类型的功能，目的是确保类型一致性和快速失败。
  + 现在可以调用 `parse_json`，以便将列中的值作为 JSON 对象进行分析，并将其扩张到多个列中。

+ **Bug 修复**
  + 修复了一个 Bug，该 Bug 在 Python 3.5.2 中导致 `set_column_types` 崩溃。
  + 修复了一个 Bug，在使用 AML 映像连接到数据存储时，该 Bug 会崩溃。

+ **更新**
  * 有关入门教程、案例研究和操作方法指南的[示例笔记本](https://aka.ms/aml-data-prep-notebooks)。

## <a name="2018-12-04-general-availability"></a>2018-12-04：正式版

Azure 机器学习服务现已公开发布。

### <a name="azure-machine-learning-compute"></a>Azure 机器学习计算
在此版本中，我们将宣告一个通过 [Azure 机器学习计算](how-to-set-up-training-targets.md#amlcompute)获得的全新托管计算体验。 此计算目标替换适用于 Azure 机器学习的 Azure Batch AI 计算。 

此计算目标：
+ 用于模型训练和批量推断
+ 是单节点到多节点计算
+ 为用户执行群集管理和作业计划
+ 默认进行自动缩放
+ 为 CPU 和 GPU 资源提供支持 
+ 允许使用低优先级 VM 来减少成本

可以通过 Python、Azure 门户或 CLI 来创建 Azure 机器学习计算。 它必须在工作区的区域中创建，不能附加到任何其他工作区。 此计算目标使用适用于运行的 Docker 容器，并将依赖项打包，以便跨所有节点复制同一环境。

> [!Warning]
> 建议创建一个新工作区来使用 Azure 机器学习计算。 尝试从现有工作区创建 Azure 机器学习计算的用户可能会看到一个错误，但这种可能性很小。 工作区中的现有计算可以继续使用，不受影响。

### <a name="azure-machine-learning-sdk-for-python-v102"></a>适用于 Python 的 Azure 机器学习 SDK v1.0.2
+ **重大更改**
  + 此版本不再支持从 Azure 机器学习创建 VM。 仍可以附加现有的云 VM 或远程本地服务器。 
  + 我们还将删除对 BatchAI 的支持，所有这些现在由 Azure 机器学习计算提供支持。

+ **New**
  + 适用于机器学习管道：
    + [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimator_step.estimatorstep?view=azure-ml-py)
    + [HyperDriveStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyper_drive_step.hyperdrivestep?view=azure-ml-py)
    + [MpiStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.mpi_step.mpistep?view=azure-ml-py)


+ **已更新**
  + 适用于机器学习管道：
    + [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py) 现在接受 runconfig
    + [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) 现在可以通过 SQL 数据源进行复制
    + SDK 中的计划功能，用于创建和更新用于运行已发布管道的计划

<!--+ **Bugs fixed**-->

### <a name="azure-machine-learning-data-prep-sdk-v052"></a>Azure 机器学习数据准备 SDK v0.5.2
+ **重大更改** 
  * `SummaryFunction.N` 已重命名为 `SummaryFunction.Count`。
  
+ **Bug 修复**
  * 通过远程运行对数据存储进行读取和写入操作时，使用最新的 AML 运行令牌。 以前，如果在 Python 中更新 AML 运行令牌，则不会使用更新的 AML 运行令牌来更新数据准备运行时。
  * 其他更清晰的错误消息
  * 当 Spark 使用 `Kryo` 序列化时，to_spark_dataframe() 将不再崩溃
  * 值计数检查器现在可以显示超过 1000 个唯一值
  * 在原始数据流没有名称的情况下，随机拆分不再失败  

+ **详细信息**
  * [Azure 机器学习数据准备 SDK](https://aka.ms/data-prep-sdk)

### <a name="docs-and-notebooks"></a>文档和笔记本
+ ML 管道
  + 全新的和更新的笔记本，适用于管道、批量范围确定和样式传输示例的入门： https://aka.ms/aml-pipeline-notebooks
  + 了解如何[创建你的第一个管道](how-to-create-your-first-pipeline.md)
  + 了解如何[使用管道运行批量预测](how-to-run-batch-predictions.md)
+ Azure 机器学习计算目标
  + [示例笔记本](https://aka.ms/aml-notebooks)现已更新，可以使用新的托管计算。
  + [了解此计算](how-to-set-up-training-targets.md#amlcompute)

### <a name="azure-portal-new-features"></a>Azure 门户：新功能
+ 在门户中创建和管理 [Azure 机器学习计算](how-to-set-up-training-targets.md#amlcompute)类型。
+ 针对 Azure 机器学习计算监视配额使用情况和[请求配额](how-to-manage-quotas.md)。
+ 实时查看 Azure 机器学习计算群集状态。
+ 增加了虚拟网络支持，方便创建 Azure 机器学习计算和 Azure Kubernetes 服务。
+ 使用现有参数重新运行已发布的管道。
+ 新的[自动化机器学习图表](how-to-track-experiments.md#auto)适用于分类模型（带模型解释功能的提升、增益、校准功能重要性图表）和回归模型（带模型解释功能的残差和功能重要性图表）。 
+ 可以在 Azure 门户中查看管道




## <a name="2018-11-20"></a>2018-11-20

### <a name="azure-machine-learning-sdk-for-python-v0180"></a>适用于 Python 的 Azure 机器学习 SDK v0.1.80

+ **重大更改** 
  * azureml.train.widgets 命名空间已移至 azureml.widgets。
  * azureml.core.compute.AmlCompute 弃用了以下类 - azureml.core.compute.BatchAICompute 和 azureml.core.compute.DSVMCompute。 后续版本中将删除后一类。 AmlCompute 类现在有一个更简单的定义，只需要 vm_size 和 max_nodes，并在提交作业时自动将集群从 0 扩展到 max_nodes。 我们的[示例笔记本](https://github.com/Azure/MachineLearningNotebooks/tree/master/training)已更新该信息，并且会提供使用示例。 我们希望你喜欢这种简化以及即将发布的版本中更多令人兴奋的功能！

### <a name="azure-machine-learning-data-prep-sdk-v051"></a>Azure 机器学习数据准备 SDK v0.5.1 

阅读[参考文档](https://aka.ms/data-prep-sdk)，了解数据准备 SDK 的详细信息。
+ **新功能**
   * 创建了一个新的 DataPrep CLI 来执行 DataPrep 包并查看数据集或数据流的数据配置文件
   * 重新设计 SetColumnType API 以提高可用性
   * 将 smart_read_file 重命名为 auto_read_file
   * 数据配置文件中现包括倾斜和峰度
   * 可采用分层采样进行采样
   * 可从包含 CSV 文件的 zip 文件中读取
   * 可使用随机拆分逐行拆分数据集（例如，进入测试训练集）
   * 可以通过调用 `.dtypes` 从数据流或数据配置文件中获取所有列数据类型
   * 可以通过调用 `.row_count` 从数据流或数据配置文件中获取行计数

+ **Bug 修复**
   * 修复长度为双线转换 
   * 修复了任何添加列后的断言 
   * 修复了 FuzzyGrouping 的问题，在某些情况下它无法检测到组
   * 修复了排序功能以遵守多列排序顺序
   * 修复和/或表达式与 `pandas` 处理它们的方式类似
   * 修复了从 dbfs 路径读取的问题
   * 使错误消息更易于理解 
   * 使用 AML 令牌在远程计算目标上读取时不再失败
   * 现不再在 Linux DSVM 上失败
   * 当非字符串值在字符串谓词中时，现不再崩溃
   * 现在，当数据流正确失败时，将处理断言错误
   * 现在支持 Azure Databricks 上的 dbutils 装载存储位置

## <a name="2018-11-05"></a>2018-11-05

### <a name="azure-portal"></a>Azure 门户 
Azure 机器学习服务的 Azure 门户具有以下更新：
  * 针对已发布的管道提供了一个新的“管道”选项卡。
  * 增加了以下支持：将现有 HDInsight 群集附加为计算目标。

### <a name="azure-machine-learning-sdk-for-python-v0174"></a>适用于 Python 的 Azure 机器学习 SDK v0.1.74

+ **重大更改** 
  * *Workspace.compute_targets、datastores、experiments、images、models 和 webservices 现在是属性而不是方法。 例如，将 Workspace.compute_targets() 替换为 Workspace.compute_targets。
  * *Run.get_context* 弃用了 *Run.get_submitted_run*。 后续版本中将删除后一种方法。
  * *PipelineData* 类现在需要使用数据存储对象作为参数而非需要 datastore_name。 类似地，*Pipeline* 接受 default_datastore 而非 default_datastore_name。

+ **新功能**
  * Azure 机器学习管道[示例 Notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline/pipeline-mpi-batch-prediction.ipynb) 现在使用 MPI 步骤。
  * Jupyter Notebook 的 RunDetails 小组件已更新，将显示管道的可视化效果。

### <a name="azure-machine-learning-data-prep-sdk-v040"></a>Azure 机器学习数据准备 SDK v0.4.0 
 
+ **新功能**
  * 向数据配置文件中添加了类型计数 
  * 现在提供了值计数和直方图
  * 数据配置文件中提供了更多百分点值
  * 汇总中提供了中值
  * 现在支持 Python 3.7
  * 将包含数据存储的数据流保存到数据准备包时，数据存储信息将持久保存为数据准备包的一部分
  * 现在支持写入到数据存储 
        
+ **修复的 Bug**
  * Linux 上现在可以正确处理 64 位无符号整数溢出
  * 修复了 smart_read 中纯文本文件的不正确文本标签
  * 字符串列类型现在显示在指标视图中
  * 类型计数已修复，它现在显示映射到单个而非各个 FieldType 的 ValueKinds
  * 将路径提供为字符串时，Write_to_csv 不再失败
  * 使用“替换”时，将“查找”保留为空将不再失败 

## <a name="2018-10-12"></a>2018-10-12

### <a name="azure-machine-learning-sdk-for-python-v0168"></a>适用于 Python 的 Azure 机器学习 SDK v0.1.68

+ **新功能**
  * 创建新工作区时的多租户支持。

+ **修复的 Bug**
  * 部署 Web 服务时，不再需要固定 pynacl 库版本。

### <a name="azure-machine-learning-data-prep-sdk-v030"></a>Azure 机器学习数据准备 SDK v0.3.0

+ **新功能**
  * 添加了方法 transform_partition_with_file(script_path)，允许用户传入 Python 文件的路径进行执行

## <a name="2018-10-01"></a>2018-10-01

### <a name="azure-machine-learning-sdk-for-python-v0165"></a>适用于 Python 的 Azure 机器学习 SDK v0.1.65
[版本 0.1.65](https://pypi.org/project/azureml-sdk/0.1.65) 包括新增功能、更多文档、bug 修复和更多[示例笔记本](https://aka.ms/aml-notebooks)。

请参阅[已知问题列表](resource-known-issues.md)了解已知 bug 和解决方法。

+ **重大更改**
  * Workspace.experiments、Workspace.models、Workspace.compute_targets、Workspace.images，Workspace.web_services 将返回字典，而以前返回列表。 请参阅 [azureml.core.Workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) API 文档。

  * 自动化机器学习从主要指标中删除了规范化的均方误差。

+ **HyperDrive**
  * 针对 Bayesian 的多种 HyperDrive bug 修复；针对获取指标调用的性能改进。 
  * 从 1.9 升级的 TensorFlow 1.10 
  * 针对冷启动的 Docker 映像优化。 
  * 作业现在报告正确状态，即便退出时显示 0 之外的错误代码。 
  * SDK 中的 RunConfig 属性验证。 
  * HyperDrive 运行对象支持类似于“常规运行：不需要传递任何参数”的取消。 
  * 小组件改进，用于维护分布式运行和 HyperDrive 运行的下拉列表值的状态。 
  * TensorBoard 和针对参数服务器修复的其他日志文件支持。 
  * 服务端上的 Intel(R) MPI 支持。 
  * 对 BatchAI 中验证期间针对分布式运行修复的参数优化的 bug 修复。 
  * 上下文管理器现可标识主要实例。 

+ **Azure 门户体验**
  * 运行详细信息中支持 log_table() 和 log_row()。 
  * 为包含 1、2 或 3 数字列和可选分类列的表格和行自动创建关系图。

+ **自动化机器学习**
  * 改进了错误处理和文档 
  * 修复了运行属性检索性能问题。 
  * 修复了连续运行的问题。 
  * 修复了集成迭代问题。
  * 修复了 MAC 操作系统上的训练挂起 bug。
  * 减少对自定义验证场景中的宏平均 PR/ROC 曲线的采样。
  * 删除额外的索引逻辑。
  * 删除了 get_output API 中的筛选器。

+ **管道**
  * 添加了方法 Pipeline.publish()，用于直接发布管道，而无需首先执行运行。   
  * 添加了方法 PipelineRun.get_pipeline_runs()，用于获取从已发布管道生成的管道运行。

+ **Project Brainwave**
  * 更新了对 FPGA 上提供的新 AI 模型的支持。

### <a name="azure-machine-learning-data-prep-sdk-v020"></a>Azure 机器学习数据准备 SDK v0.2.0
[版本 0.2.0](https://pypi.org/project/azureml-dataprep/0.2.0/) 包括以下功能和 Bug 修复：

+ **新功能**
  * 独热编码支持
  * 分位转换支持
   
+ **Bug 修复：**
  * 适用于所有 Tornado 版本，无需降级 Tornado 版本
  * 值对所有值都有意义，而不仅仅是前三个值

## <a name="2018-09-public-preview-refresh"></a>2018-09（公共预览版刷新）

新的已刷新的 Azure 机器学习版本：了解有关此版本的详细信息： https://azure.microsoft.com/blog/what-s-new-in-azure-machine-learning-service/


## <a name="next-steps"></a>后续步骤

阅读 [Azure 机器学习服务](../service/overview-what-is-azure-ml.md)的概述。
