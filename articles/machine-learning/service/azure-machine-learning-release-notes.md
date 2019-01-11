---
title: 版本新增内容
titleSuffix: Azure Machine Learning service
description: 了解 Azure 机器学习服务的最新更新以及机器学习和数据准备 Python SDK。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: reference
author: hning86
ms.author: haining
ms.reviewer: j-martens
ms.date: 12/20/2018
ms.custom: seodec18
ms.openlocfilehash: 8a67a20beff306cd23b08e1d651ab5dc4c779fd2
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2018
ms.locfileid: "53742756"
---
# <a name="azure-machine-learning-service-release-notes"></a>Azure 机器学习服务发行说明

本文介绍 Azure 机器学习服务版本。 

## <a name="2018-12-20"></a>2018-12-20： 

### <a name="azure-machine-learning-sdk-for-python-v106"></a>适用于 Python 的 Azure 机器学习 SDK v1.0.6

+ **SDK 参考文档**： https://aka.ms/aml-sdk

+ **Bug 修复**：此版本主要包含次要 Bug 修复

### <a name="azure-machine-learning-data-prep-sdk-v104"></a>Azure 机器学习数据准备 SDK v1.0.4

+ **SDK 参考文档**： https://aka.ms/data-prep-sdk

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
  + [示例笔记本] (https://aka.ms/aml-notebooks)现已更新，可以使用新的托管计算。
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
  * azureml.core.compute.AmlCompute 弃用了以下类 - azureml.core.compute.BatchAICompute 和 azureml.core.compute.DSVMCompute。 后续版本中将删除后一类。 AmlCompute 类现在有一个更简单的定义，只需要 vm_size 和 max_nodes，并在提交作业时自动将集群从 0 扩展到 max_nodes。 我们的[示例笔记本]（ https://github.com/Azure/MachineLearningNotebooks/tree/master/training) 已更新该信息，并且会提供使用示例。 我们希望你喜欢这种简化以及即将发布的版本中更多令人兴奋的功能！

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

## <a name="older-notes-sept-2017---jun-2018"></a>旧版说明：2017 年 9 月 - 2018 年 6 月
### <a name="2018-05-sprint-5"></a>2018-05（冲刺 (sprint) 5）

使用此版本的 Azure 机器学习，可以：
+ 使用量化版本 ResNet 50 将映像特征化，基于这些特征训练分类器，并[将该模型部署到 Azure 上的 FPGA](../service/how-to-deploy-fpga-web-service.md)，以便进行超低延迟推断。

+ 使用[自定义 Azure 机器学习包](../desktop-workbench/reference-python-package-overview.md)快速生成和部署高度准确的机器学习和深度学习模型

### <a name="2018-03-sprint-4"></a>2018-03（冲刺 (sprint) 4）
**版本号**：0.1.1801.24353 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;（[查找你的版本](../desktop-workbench/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number)）

下面的许多更新就是针对你的反馈的直接结果。 请持续关注！

**值得注意的新功能和更改**

- 除了支持基于远程 docker 的执行外，还支持在你自己环境中的远程 Ubuntu VM 上本机运行脚本。
- Workbench 应用中的全新环境体验，除了我们的基于 CLI 的体验外，还允许创建计算目标和运行配置。
![“环境”选项卡](media/azure-machine-learning-release-notes/environment-page.png)
- 可自定义运行历史记录报告 ![新运行历史记录报告的图像](media/azure-machine-learning-release-notes/new-run-history-reports.png)

**详细的更新**

下面列出了此冲刺 (sprint) 中 Azure 机器学习的每个组件区域中的详细更新。

#### <a name="workbench-ui"></a>Workbench UI
- 可自定义运行历史记录报告
  - 运行历史记录报告的改进图表配置
    - 可以更改使用的入口点
    - 可以添加和修改顶级筛选器 ![添加筛选器](media/azure-machine-learning-release-notes/add-filters.jpg)
    - 可以添加或修改（以及拖放和重新排列）图表和统计信息。
    ![创建新图表](media/azure-machine-learning-release-notes/configure-charts.png)

  - 运行历史记录报告的 CRUD
  - 将所有现有运行历史记录列表视图配置文件移动到了服务器端报告，其行为与从所选入口点运行的管道类似。

- “环境”选项卡
  - 轻松将新计算目标和运行配置文件添加到项目 ![新建计算目标](media/azure-machine-learning-release-notes/add-new-environments.png)
  - 使用简单的基于窗体的 UX 管理和更新配置文件
  - 用于准备执行环境的新按钮

- 对侧栏中文件列表的性能改进

#### <a name="data-preparation"></a>数据准备工作 
- Azure Machine Learning Workbench 现在允许使用已知列的名称搜索列。


#### <a name="experimentation"></a>试验
- Azure Machine Learning Workbench 现在支持在你自己的 python 或 pyspark 环境中本机运行脚本。 为实现此功能，用户在远程 VM 上创建和管理自己的环境并使用 Azure Machine Learning Workbench 对该目标运行其脚本。 请参阅[配置 Azure 机器学习试验服务](../desktop-workbench/experimentation-service-configuration.md) 

#### <a name="model-management"></a>模型管理
- 支持自定义已部署的容器：通过允许使用 apt-get 等安装外部库来支持自定义容器映像。它不再限制为 pip 可安装库。 有关详细信息，请参阅[文档](../desktop-workbench/model-management-custom-container.md)。
  - 请将 `--docker-file myDockerStepsFilename` 标志和文件名用于清单、映像或服务创建命令。
  - 基础映像是 Ubuntu，不能修改。
  - 示例命令： 
  
    ```shell
    $ az ml image create -n myimage -m mymodel.pkl -f score.py --docker-file mydockerstepsfile
    ```

### <a name="2018-01-sprint-3"></a>2018-01（冲刺 (sprint) 3） 
**版本号**：0.1.1712.18263 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;（[查找你的版本](../desktop-workbench/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number)）

下面是此冲刺 (sprint) 中的更新和改进。 其中许多更新是用户反馈的直接结果。 

### <a name="2017-12-sprint-2"></a>2017-12（冲刺 (Sprint) 2）
**版本号**：0.1.1711.15263 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;（[查找你的版本](../desktop-workbench/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number)）

此版本是 Azure 机器学习的第三个更新。 此更新包括 Workbench 应用、命令行接口 (CLI) 以及后端服务中的改进。 非常感谢你向我们发送微笑和哭脸。 下面的许多更新就是针对你的反馈的直接结果。 

### <a name="2017-11-sprint-1"></a>2017-11（冲刺 (sprint) 1） 
**版本号**：0.1.1710.31013 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;（[查找你的版本](../desktop-workbench/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number)）

在此版本中，我们改进了 Workbench 应用、CLI 及后端服务层中的安全性、稳定性及可维护性。 

### <a name="2017-10-sprint-0"></a>2017-10（冲刺 (sprint) 0） 
**版本号**：0.1.1710.31013 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;（[查找你的版本](../desktop-workbench/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number)）

此版本是自 Microsoft Ignite 2017 大会发布 Azure Machine Learning Workbench 初始公共预览版以来的首个更新。 此发行版中的主要更新是可靠性和稳定性修复。 

## <a name="next-steps"></a>后续步骤

阅读 [Azure 机器学习](../service/overview-what-is-azure-ml.md)的概述。
