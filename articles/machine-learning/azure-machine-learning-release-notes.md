---
title: 版本新增内容
titleSuffix: Azure Machine Learning
description: 了解 Azure 机器学习的最新更新以及机器学习和数据准备 Python SDK。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: jmartens
author: j-martens
ms.date: 03/10/2020
ms.openlocfilehash: 648a1ab7eac05e42a2d3ae292cccef8c0833a84d
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/01/2020
ms.locfileid: "89226805"
---
# <a name="azure-machine-learning-release-notes"></a>Azure 机器学习发行说明

本文介绍 Azure 机器学习的版本。  有关完整的 SDK 参考内容，请访问 Azure 机器学习的[**适用于 Python 的主要 SDK**](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 参考页。

请参阅[已知问题列表](resource-known-issues.md)了解已知 bug 和解决方法。

## <a name="2020-08-31"></a>2020-08-31

### <a name="azure-machine-learning-sdk-for-python-v1130"></a>用于 Python 的 Azure 机器学习 SDK 1.13。0
+ **预览功能**
  + **azureml-core**
  
    利用新的输出数据集功能，你可以写回云存储，包括 Blob、ADLS 第1代、ADLS Gen 2 和文件共享。 你可以配置输出数据的位置，如何通过装载或上传) 输出数据 (，是否注册输出数据以供将来重复使用和共享，并无缝地在管道步骤之间传递中间数据。 这可实现可再现性、共享、防止数据重复，并可提高成本效益和提高工作效率。 [了解其用法](https://docs.microsoft.com/python/api/azureml-core/azureml.data.output_dataset_config.outputfiledatasetconfig?view=azure-ml-py)
    
+ **Bug 修复与改进**
  + **azureml-automl-core**
    + 添加了 validated_ {platform} _requirements.txt 文件，用于固定 AutoML 的所有 pip 依赖项。
    + 此版本支持大于4Gb 的模型。
    + 升级后的 AutoML 依赖项： `scikit-learn` (立即 0.22.1) ， `pandas` (现在 0.25.1) ， `numpy` (现在 1.18.2) 。
  + **azureml-automl-runtime**
    + 将 text dnn 的 horovod 设置为始终使用 fp16 压缩。
    + 此版本支持大于4Gb 的模型。
    + 修复了 AutoML 在 ImportError 中失败的问题：无法导入名称 `RollingOriginValidator` 。
    + 升级后的 AutoML 依赖项： `scikit-learn` (立即 0.22.1) ， `pandas` (现在 0.25.1) ， `numpy` (现在 1.18.2) 。
  + **azureml-contrib-automl-dnn-forecasting**
    + 升级后的 AutoML 依赖项： `scikit-learn` (立即 0.22.1) ， `pandas` (现在 0.25.1) ， `numpy` (现在 1.18.2) 。
  + **azureml-contrib-fairness**
    + 提供 contrib-公平的简短说明。
  + **azureml-contrib-pipeline-steps**
    + 添加了指示此包已弃用的消息，并且用户应该改用 azureml 管道的步骤。
  + **azureml-core**
    + 添加了工作区的列表键命令。
    + 在工作区 SDK 和 CLI 中添加标记参数。
    + 修复了提交子运行时使用数据集的 bug 将因而失败 `TypeError: can't pickle _thread.RLock objects` 。
    + 正在为模型列表添加 page_count 默认/文档 ( # A1。
    + 修改 CLI&SDK 获取 adbworkspace 参数并添加工作区 adb 链接/取消链接运行程序。
    + 修复数据集中的 bug。更新导致最新的数据集版本更新，而不是调用数据集更新的版本。 
    + 修复数据集中的 bug。 get_by_name 这会显示最新数据集版本的标记，即使实际检索到特定的旧版本。
  + **azureml-interpret**
    + 将概率输出添加到 azureml 中的 shap 计分 explainers-基于原始说明中 shap_values_output 参数进行解释。
  + **azureml-pipeline-core**
    + 改进后 `PipelineOutputAbstractDataset.register` 的文档。
  + **azureml-train-automl-client**
    + 升级后的 AutoML 依赖项： `scikit-learn` (立即 0.22.1) ， `pandas` (现在 0.25.1) ， `numpy` (现在 1.18.2) 。
  + **azureml-train-automl-runtime**
    + 升级后的 AutoML 依赖项： `scikit-learn` (立即 0.22.1) ， `pandas` (现在 0.25.1) ， `numpy` (现在 1.18.2) 。
  + **azureml-train-core**
    + 用户现在必须在创建 HyperDriveConfig 时提供有效的 hyperparameter_sampling arg。 此外，已对 HyperDriveRunConfig 的文档进行了编辑，以通知用户弃用了 HyperDriveRunConfig。
    + 将 PyTorch 默认版本恢复为1.4。
    + 添加 PyTorch 1.6 & Tensorflow 2.2 映像和特选环境。

## <a name="2020-08-17"></a>2020-08-17

### <a name="azure-machine-learning-sdk-for-python-v1120"></a>用于 Python 的 Azure 机器学习 SDK 1.12。0

+ **Bug 修复与改进**
  + **azure-cli-ml**
    + 将 image_name 和 image_label 参数添加到 Model ( # A1，以启用重命名已生成的包映像。
  + **azureml-automl-core**
    + 如果在读取内容时修改内容，AutoML 将从 dataprep 引发新的错误代码。
  + **azureml-automl-runtime**
    + 当数据包含缺失值但特征化已关闭时，为用户添加了警报。
    + 修复了当数据包含 nan 并且特征化已关闭时的子运行失败。
    + 如果在读取内容时修改内容，AutoML 将从 dataprep 引发新的错误代码。
    + 更新了用于预测指标的规范化以获得粒度。
    + 在禁用 lookback 功能时，改进了预测分位数的计算。
    + 修复了 AutoML 之后计算解释时的 bool 稀疏矩阵处理。
  + **azureml-core**
    + 新方法 `run.get_detailed_status()` 现在显示当前运行状态的详细说明。 它当前仅显示状态的说明 `Queued` 。
    + 将 image_name 和 image_label 参数添加到 Model ( # A1，以启用重命名已生成的包映像。
    + `set_pip_requirements()`用于一次设置整个 pip 部分的新方法 [`CondaDependencies`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) 。
    + 允许注册不 ADLS Gen2 数据存储的无凭据。
    + 改进了尝试下载或装载不正确的数据集类型时的错误消息。
    + 更新 timeseries 数据集筛选器示例笔记本，其中包含提供筛选器优化 partition_timestamp 的更多示例。
    + 更改 sdk 和 cli，以在删除专用终结点连接时接受 subscriptionId、resourceGroup、workspaceName、peConnectionName 作为参数而不是 ArmResourceId。
    + 试验性修饰器显示类名称以便于识别。
    + 不会再根据运行自动生成模型内的资产的描述。
  + **azureml-datadrift**
    + 将 DataDriftDetector 中的 API create_from_model 标记为已弃用。
  + **azureml-dataprep**
    + 改进了尝试下载或装载不正确的数据集类型时的错误消息。
  + **azureml-pipeline-core**
    + 修复了对包含已注册数据集的管道图形进行反序列化时的错误。
  + **azureml-pipeline-steps**
    + RScriptStep 支持 RSection。
    + 已从公共 API 中删除 passthru_automl_config 参数 `AutoMLStep` ，并将其转换为仅限内部参数。
  + **azureml-train-automl-client**
    + 删除了本地异步托管环境，从 AutoML 运行。 所有本地运行都将在启动运行的环境中运行。
    + 修复了提交 AutoML 运行时未提供用户提供脚本的快照问题。
    + 修复了当数据包含 nan 并且特征化已关闭时的子运行失败。
  + **azureml-train-automl-runtime**
    + 如果在读取内容时修改内容，AutoML 将从 dataprep 引发新的错误代码。
    + 修复了提交 AutoML 运行时未提供用户提供脚本的快照问题。
    + 修复了当数据包含 nan 并且特征化已关闭时的子运行失败。
  + **azureml-train-core**
    + 添加了对指定 pip 选项的支持 (例如，将 pip 要求文件中的额外索引 url) 传递给 [`Estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) 通过 `pip_requirements_file` 参数。


## <a name="2020-08-03"></a>2020-08-03

### <a name="azure-machine-learning-sdk-for-python-v1110"></a>用于 Python 的 Azure 机器学习 SDK 1.11。0

+ **Bug 修复与改进**
  + **azure-cli-ml**
    + 修补模型框架和模型框架未传入 cli 模型注册路径中的运行对象
    + 修复 cli amlcompute identity show 命令以显示租户 id 和主体 id 
  + **azureml-train-automl-client**
    + 添加了 get_best_child ( # A1 到 AutoMLRun，用于获取 AutoML 运行的最佳子运行，而无需下载关联的模型。
    + 添加了 ModelProxy 对象，该对象允许预测或预测在远程定型环境上运行，而无需在本地下载模型。
    + AutoML 中未经处理的异常现在指向一个已知问题 HTTP 页面，此时会出现有关错误的详细信息。
  + **azureml-core**
    + 模型名称长度为255个字符。
    + 环境 get_image_details ( # A1 返回对象类型已更改。 `DockerImageDetails` 类已替换 `dict` ，可以从新的类属性获取映像详细信息。 更改是向后兼容。
    + 修复环境的 bug。 from_pip_requirements ( # A1 以保留依赖关系结构
    + 修复了一个 bug，如果在同一列表中包含 int 和 double，则 log_list 会失败。
    + 在现有工作区上启用 "专用" 链接时，请注意，如果存在与工作区关联的计算目标，则如果这些目标不在工作区专用终结点所在的虚拟网络后面，则这些目标将不起作用。
    + `as_named_input`在试验中使用数据集，并将和添加到中时，使其成为可选 `as_mount` `as_download` `FileDataset` 。 如果 `as_mount` 调用了或，则将自动生成输入名称 `as_download` 。
  + **azureml-automl-core**
    + AutoML 中未经处理的异常现在指向一个已知问题 HTTP 页面，此时会出现有关错误的详细信息。
    + 添加了 get_best_child ( # A1 到 AutoMLRun，用于获取 AutoML 运行的最佳子运行，而无需下载关联的模型。
    + 添加了 ModelProxy 对象，该对象允许预测或预测在远程定型环境上运行，而无需在本地下载模型。
  + **azureml-pipeline-steps**
    + 添加了 `enable_default_model_output` 并 `enable_default_metrics_output` 标志到 `AutoMLStep` 。 这些标志可用于启用/禁用默认输出。


## <a name="2020-07-20"></a>2020-07-20

### <a name="azure-machine-learning-sdk-for-python-v1100"></a>适用于 Python 的 Azure 机器学习 SDK v1.10.0

+ **Bug 修复与改进**
  + **azureml-automl-core**
    + 使用 AutoML 时，如果向 AutoMLConfig 对象传递了一个路径但该路径不存在，系统会自动创建该路径。
    + 用户现在可以使用 `freq` 参数指定一个用于预测任务的时序频率。
  + **azureml-automl-runtime**
    + 使用 AutoML 时，如果向 AutoMLConfig 对象传递了一个路径但该路径不存在，系统会自动创建该路径。
    + 用户现在可以使用 `freq` 参数指定一个用于预测任务的时序频率。
    + AutoML 预测现在支持滚动评估，该评估适用于其测试或验证集长度长于输入范围的用例。已知的 y_pred 值用作预测上下文。
  + **azureml-core**
    + 如果在运行中未从数据存储下载任何文件，则会输出警告消息。
    + 向 `Datastore.register_azure_sql_database method` 添加了 `skip_validation` 的文档。
    + 用户需要升级到 sdk v1.10.0 或更高版本，才能创建自动批准的专用终结点。 这包括位于 VNet 后面的可用 Notebook 资源。
    + 在获取工作区的响应中公开 NotebookInfo。
    + 进行了更改，可以在远程运行时成功地通过调用操作列出计算目标和获取计算目标。 用于获取计算目标和列出工作区计算目标的 Sdk 函数现在可在远程运行中使用。
    + 向 azureml.core.image 类的类描述添加了弃用消息。
    + 如果工作区专用终结点创建失败，则会引发异常并清除工作区和依赖的资源。
    + 支持工作区 sku 以工作区更新方法进行升级。
  + **azureml-datadrift**
    + 将 matplotlib 版本从 3.0.2 更新为 3.2.1 以支持 python 3.8。
  + **azureml-dataprep**
    + 添加了对具有 `Range` 或 `Head` 请求的 web url 数据源的支持。 
    + 提高了文件数据集装载和下载的稳定性。
  + **azureml-train-automl-client**
    + 修复了从 setuptools 中删除 `RequirementParseError` 时的相关问题。
    + 对于使用“compute_target='local'”提交的本地运行，请使用 docker 而非 conda
    + 更正了输出到控制台的迭代持续时间。 以前，迭代持续时间有时输出为运行结束时间减去运行创建时间。 它已进行更正，等于运行结束时间减去运行开始时间。
    + 使用 AutoML 时，如果向 AutoMLConfig 对象传递了一个路径但该路径不存在，系统会自动创建该路径。
    + 用户现在可以使用 `freq` 参数指定一个用于预测任务的时序频率。
  + **azureml-train-automl-runtime**
    + 改进了最佳模型解释失败时的控制台输出。
    + 已将“blacklist_models”输入参数重命名为“blocked_models”。
      + 已将“whitelist_models”输入参数重命名为“allowed_models”。
    + 用户现在可以使用 `freq` 参数指定一个用于预测任务的时序频率。

  
## <a name="2020-07-06"></a>2020-07-06

### <a name="azure-machine-learning-sdk-for-python-v190"></a>适用于 Python 的 Azure 机器学习 SDK v1.9.0

+ **Bug 修复与改进**
  + **azureml-automl-core**
    + 在 AutoML 自动生成的评分脚本中，已将 get_model_path() 替换为 AZUREML_MODEL_DIR 环境变量。 还添加了遥测来跟踪 init() 期间的故障。
    + 删除了在 AutoMLConfig 中指定 `enable_cache` 的功能
    + 修复了运行可能会在特定的预测运行期间失败并出现服务错误的 bug
    + 改进了在 `get_output` 期间针对特定模型的错误处理
    + 对于具有 y 转换器的分类，修复了对 fitted_model.fit(X, y) 的调用
    + 启用了自定义的正向填充 imputer，用于预测任务
    + 将使用新的 ForecastingParameters 类，而不是 dict 格式的预测参数
    + 改进了目标延迟自动检测
    + 通过 BERT 添加了可用性有限的多节点、多 gpu 分布式特征化
  + **azureml-automl-runtime**
    + Prophet 现在执行加法（而非乘法）周期性建模。
    + 修复了当短时间粒度的频率与长时间粒度的频率不同时会导致运行失败的问题。
  + **azureml-contrib-automl-dnn-vision**
    + 收集用于训练和评分的系统/gpu 统计信息和日志平均值
  + **azureml-contrib-mir**
    + 在 ManagedInferencing 中添加了对 enable-app-insights 标志的支持
  + **azureml-core**
    + 为这些 API 提供了一个 validate 参数，允许在无法从当前计算访问数据源的情况下跳过验证。
      + TabularDataset.time_before(end_time, include_boundary=True, validate=True)
      + TabularDataset.time_after(start_time, include_boundary=True, validate=True)
      + TabularDataset.time_recent(time_delta, include_boundary=True, validate=True)
      + TabularDataset.time_between(start_time, end_time, include_boundary=True, validate=True)
    + 为模型列表添加了框架筛选支持，并且在笔记本中又重新添加了 NCD automl 示例
    + 对于 Datastore.register_azure_blob_container 和 Datastore.register_azure_file_share（仅限支持 SAS 令牌的选项），我们更新了 `sas_token` 字段的文档字符串，使之包括典型的读取和写入方案的最低权限要求。
    + 在 ws.get_mlflow_tracking_uri() 中弃用了 _with_auth 参数
  + **azureml-mlflow**
    + 添加了相关支持，允许通过 AzureML-MLflow 部署本地文件:// 模型
    + 在 ws.get_mlflow_tracking_uri() 中弃用了 _with_auth 参数
  + **azureml-opendatasets**
    + 最近发布的 Covid-19 跟踪数据集现已随 SDK 一起提供
  + **azureml-pipeline-core**
    + 当“azureml-defaults”未作为 pip-dependency 的一部分包括在内时会发出注销警告
    + 改进了备注呈现。
    + 添加了相关支持，允许在将带分隔符的文件分析为 PipelineOutputFileDataset 时使用带引号的换行符。
    + 弃用了 PipelineDataset 类。 有关详细信息，请参阅 https://aka.ms/dataset-deprecation。 若要了解如何在管道中使用数据集，请参阅 https://aka.ms/pipeline-with-dataset 。
  + **azureml-pipeline-steps**
    + 更新了 azureml-pipeline-steps 的文档。
    +  在 ParallelRunConfig 的 `load_yaml()` 中添加了相关支持，允许用户在配置的其余部分中或在单独的文件中定义内联环境
  + **azureml-train-automl-client**。
    + 删除了在 AutoMLConfig 中指定 `enable_cache` 的功能
  + **azureml-train-automl-runtime**
    + 通过 BERT 添加了可用性有限的多节点、多 gpu 分布式特征化。
    + 在基于 ADB 的自动化机器学习运行中针对不兼容程序包添加了错误处理。
  + **azureml-widgets**
    + 更新了 azureml-widgets 的文档。

  
## <a name="2020-06-22"></a>2020-06-22

### <a name="azure-machine-learning-sdk-for-python-v180"></a>适用于 Python 的 Azure 机器学习 SDK v1.8.0
  
  + **预览功能**
    + **azureml-contrib-fairness** `azureml-contrib-fairness` 包提供在开源公平性评估与不公平性缓解包 [Fairlearn](https://fairlearn.github.io) 和 Azure 机器学习工作室之间进行的集成。 具体说来，可以通过此包在 AzureML 运行过程中上传模型公平性评估仪表板并将其显示在 Azure 机器学习工作室中

+ **Bug 修复与改进**
  + **azure-cli-ml**
    + 支持获取 init 容器的日志。
    + 添加了新的用于管理 ComputeInstance 的 CLI 命令
  + **azureml-automl-core**
    + 用户现在能够为时序任务启用堆栈系综迭代，并发出一条指示该迭代可能会过度拟合的警告。
    + 添加了新类型的用户异常 `azureml.automl.core.shared.exceptions.CacheStoreCorruptedException`，此异常在缓存存储内容被篡改的情况下引发
  + **azureml-automl-runtime**
    + 如果用户禁用特征化，则不会再启用类均衡扫描。  
  + **azureml-contrib-itp**
    + 支持 CmAks 计算类型。 你可以将自己的 AKS 群集附加到工作区来完成训练作业。
  + **azureml-contrib-notebook**
    + 对 azureml-contrib-notebook 包进行了文档改进。
  + **azureml-contrib-pipeline-steps**
    + 对 azureml-contrib--pipeline-steps 包进行了文档改进。
  + **azureml-core**
    + 添加了 set_connection、get_connection、list_connections、delete_connection 函数，方便客户对工作区连接资源进行操作
    + 对 azureml-coore/azureml.exceptions 包进行了文档更新。
    + 对 azureml-core 包进行了文档更新。
    + 对 ComputeInstance 类进行了文档更新。
    + 对 azureml-core/azureml.core.compute 包进行了文档改进。
    + 对 azureml-core 中与 webservice 相关的类进行了文档改进。
    + 支持使用用户选择的数据存储来存储分析数据
    + 为模型列表 API 添加了 expand 和 page_count 属性
    + 修复了这样的 bug：删除 overwrite 属性会导致提交的运行失败，并出现反序列化错误。
    + 修复了在下载或装载对单个文件的 FileDataset 引用时出现的不一致文件夹结构。
    + 加载 parquet 文件数据集 to_spark_dataframe 的操作现在更快，并支持所有 parquet 和 Spark SQL 数据类型。
    + 支持获取 init 容器的日志。
    + 现在，AutoML 运行会被标记为“并行运行步骤”的子运行。
  + **azureml-datadrift**
    + 对 azureml-contrib-notebook 包进行了文档改进。
  + **azureml-dataprep**
    + 加载 parquet 文件数据集 to_spark_dataframe 的操作现在更快，并支持所有 parquet 和 Spark SQL 数据类型。
    + 对于 to_pandas_dataframe，改进了出现 OutOfMemory 问题时的内存处理。
  + **azureml-interpret**
    + 已将 azureml-interpret 升级为使用 interpret-community 版本 0.12.*
  + **azureml-mlflow**
    + 对 azureml-mlflow 进行了文档改进。
    + 添加对用于 MLFlow 的 AML 模型注册表的支持。
  + **azureml-opendatasets**
    + 添加了对 Python 3.8 的支持
  + **azureml-pipeline-core**
    + 更新了 `PipelineDataset` 的文档，清楚地表明它是一个内部类。
    + 进行了 ParallelRunStep 更新，允许一个参数接受多个值，例如："--group_column_names"、"Col1"、"Col2"、"Col3"
    + 删除了在管道中通过 AutoMLStep 使用中间数据时的 passthru_automl_config 要求。
  + **azureml-pipeline-steps**
    + 对 azureml-pipeline-steps 包进行了文档改进。
    + 删除了在管道中通过 AutoMLStep 使用中间数据时的 passthru_automl_config 要求。
  + **azureml-telemetry**
    + 对 azureml-telemetry 进行了文档改进。
  + **azureml-train-automl-client**
    + 修复了以下 bug：在 `AutoMLConfig` 对象上调用 `experiment.submit()` 两次导致不同的行为。
    + 用户现在能够为时序任务启用堆栈系综迭代，并发出一条指示该迭代可能会过度拟合的警告。
    + 更改了 AutoML 运行行为，在服务引发用户错误时会引发 UserErrorException
    + 修复了一个 bug。在远程计算目标上进行 AutoML 试验时，该 bug 会导致 azureml_automl.log 无法生成，或者会导致缺少日志。
    + 对于具有不均衡类的分类数据集，我们将应用权重平衡（如果功能 sweeper 确定对于子采样的数据），则权重均衡会按特定阈值提高分类任务的性能。
    + 现在，AutoML 运行会被标记为“并行运行步骤”的子运行。
  + **azureml-train-automl-runtime**
    + 更改了 AutoML 运行行为，在服务引发用户错误时会引发 UserErrorException
    + 现在，AutoML 运行会被标记为“并行运行步骤”的子运行。

  
## <a name="2020-06-08"></a>2020-06-08

### <a name="azure-machine-learning-sdk-for-python-v170"></a>适用于 Python 的 Azure 机器学习 SDK v1.7.0

+ **Bug 修复与改进**
  + **azure-cli-ml**
    + 通过清理 cli 命令和程序包依赖项从 mir contrib 中删除了模型分析功能，现在模型分析功能在核心中提供。
    + 将最低 Azure CLI 版本升级到 2.3.0
  + **azureml-automl-core**
    + 归功于自定义转换器参数，特征化步骤 fit_transform() 现在可以提供更好的异常消息。
    + 在自动化 ML 中为深度学习转换器模型（例如 BERT）增加了对多种语言的支持。
    + 从文档中删除了弃用的 lag_length 参数。
    + 改进了预测参数文档。 弃用了 lag_length 参数。
  + **azureml-automl-runtime**
    + 修复了在预测/测试时间内某个分类列为空时引发的错误。
    + 修复了当启用了回溯功能并且数据包含短粒度时发生的运行故障。
    + 修复了当延迟或滚动窗口设置为“auto”时出现重复的时间索引错误消息的问题。
    + 修复了包含回溯功能的、基于数据集的 Prophet 和 Arima 模型的问题。
    + 在预测任务的日期时间以外的列中增加了对 1677-09-21 之前或 2262-04-11 之后的日期的支持。 改进了错误消息。
    + 改进了预测参数文档。 弃用了 lag_length 参数。
    + 归功于自定义转换器参数，特征化步骤 fit_transform() 现在可以提供更好的异常消息。
    + 在自动化 ML 中为深度学习转换器模型（例如 BERT）增加了对多种语言的支持。
    + 导致某些 OSError 的缓存操作会引发用户错误。
    + 增加了检查来确保训练和验证数据具有相同的列数和列集
    + 修复了当数据包含引号时自动生成的 AutoML 评分脚本的问题
    + 启用了对 AutoML Prophet 模型和包含 Prophet 模型的系综模型的说明。
    + 最近的一个客户问题揭示了一个实时站点 bug：即使没有正确启用类均衡逻辑，我们也会随着类均衡扫描记录消息。 将通过此 PR 删除这些日志/消息。
  + **azureml-cli-common**
    + 通过清理 cli 命令和程序包依赖项从 mir contrib 中删除了模型分析功能，现在模型分析功能在核心中提供。
  + **azureml-contrib-reinforcementlearning**
    + 负载测试工具
  + **azureml-core**
    + 有关 Script_run_config.py 的文档变更
    + 修复了列显 submit-pipeline CLI 的运行输出时的一个 bug
    + 有关 azureml-core/azureml.data 的文档改进
    + 修复了使用 hdfs getconf 命令检索存储帐户时出现的问题
    + 改进了 register_azure_blob_container 和 register_azure_file_share 文档
  + **azureml-datadrift**
    + 改进了禁用和启用数据集偏移监视器的实现
  + **azureml-interpret**
    + 在解释客户端中，在从项目上传时在进行 json 序列化之前删除 NaN 或 Inf
    + 更新到了最新版本的 interpret-community，以通过许多功能和类改进全局解释的内存不足错误
    + 向解释上传添加了 true_ys 可选参数，以在工作室 UI 中启用更多功能
    + 改进了 download_model_explanations() 和 list_model_explanations() 的性能
    + 对笔记本进行了一些有助于调试的小调整
  + **azureml-opendatasets**
    + azureml-opendatasets 需要 azureml-dataprep 1.4.0 或更高版本。 添加了当检测到的版本较低时要显示的警告
  + **azureml-pipeline-core**
    + 此更改允许用户在调用 module.Publish_python_script 时为 moduleVersion 提供可选的 runconfig。
    + “启用节点帐户”可以用作 azureml.pipeline.steps 的 ParallelRunStep 中的一个管道参数。
  + **azureml-pipeline-steps**
    + 此更改允许用户在调用 module.Publish_python_script 时为 moduleVersion 提供可选的 runconfig。
  + **azureml-train-automl-client**
    + 在自动化 ML 中为深度学习转换器模型（例如 BERT）增加了对多种语言的支持。
    + 从文档中删除了弃用的 lag_length 参数。
    + 改进了预测参数文档。 弃用了 lag_length 参数。
  + **azureml-train-automl-runtime**
    + 启用了对 AutoML Prophet 模型和包含 Prophet 模型的系综模型的说明。
    + 更新了 azureml-train-automl-* 程序包的文档。
  + **azureml-train-core**
    + 在 PyTorch 估算器中支持 TensorFlow 版本 2.1
    + 改进了 azureml-train-core 程序包。
  
## <a name="2020-05-26"></a>2020-05-26

### <a name="azure-machine-learning-sdk-for-python-v160"></a>适用于 Python 的 Azure 机器学习 SDK v1.6.0

+ **新功能**
  + **azureml-automl-runtime**
    + AutoML 预测现在支持超出预先指定的最大范围进行客户预测，无需重新训练模型。 当预测目标在时间上比指定的最大范围更晚时，forecast() 函数仍会使用递归操作模式将点预测扩展到更晚的日期。 如需对此新功能的解释，请参阅相关[文件夹](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)中“forecasting-forecast-function”笔记本的“超出最大范围进行预测”部分。
  
  + **azureml-pipeline-steps**
    + ParallelRunStep 现已发布，是 **azureml-pipeline-steps** 程序包的一部分。 **azureml-contrib-pipeline-steps** 程序包中的现有 ParallelRunStep 已弃用。 公共预览版本中的更改：
      + 添加了可选的可配置参数 `run_max_try` 来控制对任意给定批的运行方法的最大调用次数，默认值为 3。
      + 不再自动生成 PipelineParameter。 可以将以下可配置值显式设置为 PipelineParameter。
        + mini_batch_size
        + node_count
        + process_count_per_node
        + logging_level
        + run_invocation_timeout
        + run_max_try
      + process_count_per_node 的默认值已更改为 1。 用户应该微调此值以获得更好的性能。 最佳做法是设置为一个节点具有的 GPU 或 CPU 数。
      + ParallelRunStep 不注入任何程序包，用户需要在环境定义中包括 **azureml-core** 和 **azureml-dataprep[pandas, fuse]** 程序包。 如果通过 user_managed_dependencies 使用了自定义 Docker 映像，则用户需要在该映像上安装 conda。
      
+ **重大更改**
  + **azureml-pipeline-steps**
    + 不再使用 azureml.dprep.Dataflow 作为 AutoMLConfig 的有效输入类型
  + **azureml-train-automl-client**
    + 不再使用 azureml.dprep.Dataflow 作为 AutoMLConfig 的有效输入类型

+ **Bug 修复与改进**
  + **azureml-automl-core**
    + 修复了以下 bug：在 `get_output` 期间可能会输出一条警告，要求用户降级客户端。
    + 已将 Mac 更新为依赖于 cudatoolkit=9.0，因为它在版本 10 上尚不可用。
    + 在远程计算上训练时，删除了对 phrophet 和 xgboost 模型的限制。
    + 改进了 AutoML 中的日志记录
    + 改进了预测任务中自定义特征化的错误处理。
    + 添加了允许用户包含滞后功能来生成预测的功能。
    + 更新了错误消息以正确显示用户错误。
    + 支持将 cv_split_column_names 与 training_data 一起使用
    + 更新了异常消息日志记录和回溯。
  + **azureml-automl-runtime**
    + 为预测缺失值插补启用护栏。
    + 改进了 AutoML 中的日志记录
    + 为数据准备异常添加了精细的错误处理
    + 在远程计算上训练时，删除了对 phrophet 和 xgboost 模型的限制。
    + `azureml-train-automl-runtime` 和 `azureml-automl-runtime` 已更新了 `pytorch`、`scipy` 和 `cudatoolkit` 的依赖项。 我们现在支持 `pytorch==1.4.0`、`scipy>=1.0.0,<=1.3.1` 和 `cudatoolkit==10.1.243`。
    + 改进了预测任务中自定义特征化的错误处理。
    + 改进了预测数据集频率检测机制。
    + 修复了基于某些数据集进行 Prophet 模型训练时出现的问题。
    + 改进了在预测期间对最大范围的自动检测。
    + 添加了允许用户包含滞后功能来生成预测的功能。
    +  在预测函数中添加了功能，现在可以超出已训练范围提供预测，无需重新训练预测模型。
    + 支持将 cv_split_column_names 与 training_data 一起使用
  + **azureml-contrib-automl-dnn-forecasting**
    + 改进了 AutoML 中的日志记录
  + **azureml-contrib-mir**
    + 在 ManagedInferencing 中增加了对 Windows 服务的支持
    + 删除了旧的 MIR 工作流，例如附加 MIR 计算、SingleModelMirWebservice 类 - 清除了置于 contrib-mir 程序包中的模型分析功能
  + **azureml-contrib-pipeline-steps**
    + 对 YAML 支持进行了微小修复
    + ParallelRunStep 已发布到正式版 - azureml.contrib.pipeline.steps 发布了弃用通知并移到了 azureml.pipeline.steps
  + **azureml-contrib-reinforcementlearning**
    + RL 负载测试工具
    + RL 估算器具有智能默认值
  + **azureml-core**
    + 删除了旧的 MIR 工作流，例如附加 MIR 计算、SingleModelMirWebservice 类 - 清除了置于 contrib-mir 程序包中的模型分析功能
    + 修复了当分析失败时提供给用户的信息：包括了请求 ID，并重新编写了消息以使其更有意义。 向分析运行器添加了新的分析工作流
    + 显著改进了数据集执行失败时的错误文本。
    + 添加了工作区专用链接 CLI 支持。
    + 向 `Dataset.Tabular.from_json_lines_files` 添加了可选参数 `invalid_lines`，该参数允许指定如何处理包含无效 JSON 的行。
    + 在下一版本中，我们将弃用根据运行来创建计算的功能。 建议将实际的 Amlcompute 群集创建为持久性计算目标，并使用群集名称作为运行配置中的计算目标。 请参阅此处的示例笔记本：aka.ms/amlcomputenb
    + 显著改进了数据集执行失败时的错误消息。
  + **azureml-dataprep**
    + 更明确地发出警告来要求升级 pyarrow 版本。
    + 改进了执行数据流失败时的错误处理和返回的消息。
  + **azureml-interpret**
    + 更新了 azureml-interpret 程序包的文档。
    + 修复了可解释性程序包和笔记本，使之可以与最新的 sklearn 更新兼容
  + **azureml-opendatasets**
    + 当没有数据返回时，返回 None。
    + 改进了 to_pandas_dataframe 的性能。
  + **azureml-pipeline-core**
    + 快速修复了造成 YAML 加载中断的 ParallelRunStep
    + ParallelRunStep 已发布到正式版 - azureml.contrib.pipeline.steps 发布了弃用通知并移到了 azureml.pipeline.steps - 新功能包括：1. 数据集作为 PipelineParameter 2. 新参数 run_max_retry 3. 可配置的 append_row 输出文件名
  + **azureml-pipeline-steps**
    + 弃用了 azureml.dprep.Dataflow，它不再是输入数据的有效类型。
    + 快速修复了造成 YAML 加载中断的 ParallelRunStep
    + ParallelRunStep 已发布到正式版 - azureml.contrib.pipeline.steps 发布了弃用通知并移到了 azureml.pipeline.steps - 新功能包括：
      + 数据集作为 PipelineParameter
      + 新参数 run_max_retry
      + 可配置的 append_row 输出文件名
  + **azureml-telemetry**
    + 更新了异常消息日志记录和回溯。
  + **azureml-train-automl-client**
    + 改进了 AutoML 中的日志记录
    + 更新了错误消息以正确显示用户错误。
    + 支持将 cv_split_column_names 与 training_data 一起使用
    + 弃用了 azureml.dprep.Dataflow，它不再是输入数据的有效类型。
    + 已将 Mac 更新为依赖于 cudatoolkit=9.0，因为它在版本 10 上尚不可用。
    + 在远程计算上训练时，删除了对 phrophet 和 xgboost 模型的限制。
    + `azureml-train-automl-runtime` 和 `azureml-automl-runtime` 已更新了 `pytorch`、`scipy` 和 `cudatoolkit` 的依赖项。 我们现在支持 `pytorch==1.4.0`、`scipy>=1.0.0,<=1.3.1` 和 `cudatoolkit==10.1.243`。
    + 添加了允许用户包含滞后功能来生成预测的功能。
  + **azureml-train-automl-runtime**
    + 改进了 AutoML 中的日志记录
    + 为数据准备异常添加了精细的错误处理
    + 在远程计算上训练时，删除了对 phrophet 和 xgboost 模型的限制。
    + `azureml-train-automl-runtime` 和 `azureml-automl-runtime` 已更新了 `pytorch`、`scipy` 和 `cudatoolkit` 的依赖项。 我们现在支持 `pytorch==1.4.0`、`scipy>=1.0.0,<=1.3.1` 和 `cudatoolkit==10.1.243`。
    + 更新了错误消息以正确显示用户错误。
    + 支持将 cv_split_column_names 与 training_data 一起使用
  + **azureml-train-core**
    + 添加了一组新的 HyperDrive 特定异常。 azureml.train.hyperdrive 现在将引发详细异常。
  + **azureml-widgets**
    + AzureML 小组件不显示在 JupyterLab 中
  

## <a name="2020-05-11"></a>2020-05-11

### <a name="azure-machine-learning-sdk-for-python-v150"></a>适用于 Python 的 Azure 机器学习 SDK v1.5.0

+ **新功能**
  + **预览功能**
    + **azureml-contrib-reinforcementlearning**
        + Azure 机器学习将发布对使用 [Ray](https://ray.io) 框架的强化学习的预览版支持。 `ReinforcementLearningEstimator` 支持在 Azure 机器学习中跨 GPU 和 CPU 计算目标训练强化学习代理。

+ **Bug 修复与改进**
  + **azure-cli-ml**
    + 修复了在我以前的 PR 中意外遗留的警告日志。 此日志用于调试，是意外遗留的。
    + Bug 修复：在分析过程中向客户端告知部分失败
  + **azureml-automl-core**
    + 当数据集具有多个时序时，通过为时序启用并行拟合，加快 AutoML 预测中的 Prophet/AutoArima 模型。 为了从此新功能中获益，建议你在 AutoMLConfig 中设置 "max_cores_per_iteration = -1"（即，使用所有可用的 CPU 核心）。
    + 修复了控制台界面中有关输出护栏的 KeyError
    + 修复了有关 experimentation_timeout_hours 的错误消息
    + 弃用了 AutoML 的 Tensorflow 模型。
  + **azureml-automl-runtime**
    + 修复了有关 experimentation_timeout_hours 的错误消息
    + 修复了尝试从缓存存储进行反序列化操作时的未分类异常
    + 当数据集具有多个时序时，通过为时序启用并行拟合，加快 AutoML 预测中的 Prophet/AutoArima 模型。
    + 对于测试/预测集不包含训练集中的某个粒度的数据集，通过启用滚动窗口修复了预测问题。
    + 改进了对缺失数据的处理
    + 修复了基于数据集进行预测时，包含时序的预测间隔在时间上不对齐的问题。
    + 为预测任务添加了更好的数据形态验证。
    + 改进了频率检测。
    + 当无法生成预测任务的交叉验证折叠时，会创建更好的错误消息。
    + 修复了控制台界面以正确输出缺失值护栏。
    + 在 AutoMLConfig 中对 cv_split_indices 输入强制执行数据类型检查。
  + **azureml-cli-common**
    + Bug 修复：在分析过程中向客户端告知部分失败
  + **azureml-contrib-mir**
    + 添加了一个 azureml.contrib.mir.RevisionStatus 类，它中继有关当前部署的 MIR 版本和用户指定的最新版本的信息。 此类包含在 MirWebservice 对象中的“deployment_status”属性下。
    + 为 MirWebservice 类型及其子类 SingleModelMirWebservice 的 Webservices 启用了更新。
  + **azureml-contrib-reinforcementlearning**
    + 增加了对 Ray 0.8.3 的支持
    + AmlWindowsCompute 仅支持将 Azure 文件存储作为装载的存储
    + 已将 health_check_timeout 重命名为 health_check_timeout_seconds
    + 修复了一些类/方法说明。
  + **azureml-core**
    + 在美国政府云和中国云中启用了 WASB -> Blob 转换。
    + 修复了 bug，允许读取者角色使用 az ml run CLI 命令获取运行信息
    + 删除了使用输入数据集进行 Azure ML 远程运行期间的不必要日志记录。
    + RCranPackage 现在支持 CRAN 程序包版本的“version”参数。
    + Bug 修复：在分析过程中向客户端告知部分失败
    + 为 azureml-core 添加了欧洲样式的的浮点处理。
    + 在 Azure ml sdk 中启用了工作区专用链接功能。
    + 使用 `from_delimited_files` 创建 TabularDataset 时，可以通过设置布尔参数 `empty_as_string` 来指定应将空值加载为 None 还是加载为空字符串。
    + 为数据集添加了欧洲样式的的浮点处理。
    + 改进了有关数据集装载失败的错误消息。
  + **azureml-datadrift**
    + SDK 中的数据偏移结果查询有一个 bug：未区分最小值、最大值和平均特征指标，导致值重复。 我们已修复了此 bug，在指标名称前加上了前缀 target 或 baseline。 之前：重复的 min、max、mean。 之后：target_min、target_max、target_mean、baseline_min、baseline_max、baseline_mean。
  + **azureml-dataprep**
    + 在确保数据传送所需的 .NET 依赖项时，改进了对写受限 Python 环境的处理。
    + 修复了在具有前导空记录的文件上创建数据流的问题。
    + 为类似于 `to_pandas_dataframe` 的 `to_partition_iterator` 添加了错误处理选项。
  + **azureml-interpret**
    + 降低了解释路径长度限制，以降低超出 Windows 限制的可能性
    + 对使用线性代理模型通过模拟解释器创建的稀疏解释进行了 Bug 修复。
  + **azureml-opendatasets**
    + 修复了将 MNIST 的列分析为字符串（应当为 int）的问题。
  + **azureml-pipeline-core**
    + 当使用一个嵌入到 ModuleStep 中的模块时，允许使用 regenerate_outputs 选项。
  + **azureml-train-automl-client**
    + 弃用了 AutoML 的 Tensorflow 模型。
    + 修补用户允许列出本地模式下不支持的算法
    + 修复了 AutoMLConfig 的文档。
    + 在 AutoMLConfig 中对 cv_split_indices 输入强制执行数据类型检查。
    + 修复了 show_output 中的 AutoML 运行失败问题
  + **azureml-train-automl-runtime**
    + 修复了系综迭代中的一个 bug，该 bug 阻止模型下载超时成功启动。
  + **azureml-train-core**
    + 修复了 azureml.train.dnn.Nccl 类中的拼写错误。
    + 在 PyTorch 估算器中支持 PyTorch 版本 1.5
    + 修复了使用训练框架估算器时无法在 Fairfax 区域获取框架映像的问题

  
## <a name="2020-05-04"></a>2020-05-04
**全新笔记本体验**

现在，你可以直接在 Azure 机器学习的工作室 Web 体验中创建、编辑和共享机器学习笔记本和文件。 你可以在这些笔记本中使用 [Azure 机器学习 Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 中提供的所有类和方法。请从[此处](https://docs.microsoft.com/azure/machine-learning/how-to-run-jupyter-notebooks)开始

**引入的新功能：**

+ 改进了 VS Code 使用的编辑器（Monaco 编辑器） 
+ UI/UX 改进
+ 单元格工具栏
+ 新的笔记本工具栏和计算控件
+ 笔记本状态栏 
+ 内联内核切换
+ R 支持
+ 辅助功能和本地化改进
+ 命令面板
+ 更多键盘快捷方式
+ 自动保存
+ 改进了性能和可靠性

从工作室访问以下基于 Web 的创作工具：
    
| 基于 Web 的工具  |     说明  | 版本 | 
|---|---|---|
| Azure 机器学习工作室笔记本   |     笔记本文件的一流创作工具，支持 Azure ML Python SDK 中提供的所有操作。 | 基本和企业版  |   

## <a name="2020-04-27"></a>2020-04-27

### <a name="azure-machine-learning-sdk-for-python-v140"></a>适用于 Python 的 Azure 机器学习 SDK v1.4.0

+ **新功能**
  + AmlCompute 群集现在支持预配时在群集上设置托管标识。 你只需指定是要使用系统分配的标识还是用户分配的标识，并在使用后者的情况下传递一个 identityId。 然后，你可以设置访问各种资源（例如存储或 ACR）的权限，使用计算的标识来安全地访问数据，而不是使用 AmlCompute 现在采用的基于令牌的方法。 有关参数的详细信息，请查看 SDK 参考。
  

+ **重大更改**
  + AmlCompute 群集过去支持“基于运行的创建”预览版功能，我们计划在两周内弃用该功能。 你可以继续使用 Amlcompute 类一如既往地创建持久计算目标。但在不久的将来，我们将不支持在运行配置中将标识符“amlcompute”指定为计算目标这一特定方法。 

+ **Bug 修复与改进**
  + **azureml-automl-runtime**
    + 计算列中的唯一值的数目时，启用了对不可哈希类型的支持。
  + **azureml-core**
    + 改进了使用 TabularDataset 从 Azure Blob 存储进行读取时的稳定性。
    + 改进了 `Datastore.register_azure_blob_store` 的 `grant_workspace_msi` 参数的文档。
    + 修复了 `datastore.upload` 的 bug，支持以 `/` 或 `\` 结尾的 `src_dir` 参数。
    + 添加了当尝试上传到没有访问密钥或 SAS 令牌的 Azure Blob 存储数据存储时显示的可操作错误消息。
  + **azureml-interpret**
    + 为上传的解释的可视化数据添加了文件大小上限。
  + **azureml-train-automl-client**
    + 显式检查 AutoMLConfig 的 label_column_name 和 weight_column_name 参数是否为字符串类型。
  + **azureml-contrib-pipeline-steps**
    + ParallelRunStep 现在支持将数据集用作管道参数。 用户可以使用示例数据集来构造管道，并且可以为新的管道运行更改相同类型（文件或表格）的输入数据集。

  
## <a name="2020-04-13"></a>2020-04-13

### <a name="azure-machine-learning-sdk-for-python-v130"></a>适用于 Python 的 Azure 机器学习 SDK v1.3.0

+ **Bug 修复与改进**
  + **azureml-automl-core**
    + 围绕训练后操作添加了其他遥测。
    + 对长度大于 100 的序列使用条件平方和 (CSS) 训练，从而加快自动化 ARIMA 训练。 注意，使用的长度存储为 /src/azureml-automl-core/azureml/automl/core/shared/constants.py 的 TimeSeriesInternal 类中的常量 ARIMA_TRIGGER_CSS_TRAINING_LENGTH
    + 改进了预测运行的用户日志记录，现在日志中会显示有关当前正在运行的阶段的详细信息
    + 不允许将 target_rolling_window_size 设置为小于 2 的值
  + **azureml-automl-runtime**
    + 改进了发现重复时间戳时显示的错误消息。
    + 不允许将 target_rolling_window_size 设置为小于 2 的值。
    + 修复了延迟插补失败。 此问题是由于根据季节分解序列所需的观测数不足而导致的。 现在将使用“去季节化”数据来计算部分自动更正函数 (PACF)，以确定延迟长度。
    + 通过特征化配置为预测任务启用了列用途特征化自定义。现在支持将“数值”和“类别”用作预测任务的列用途。
    + 通过特征化配置为预测任务启用了删除列特征化自定义。
    + 通过特征化配置为预测任务启用了插补自定义。现在支持为目标列进行常量值插补，支持为训练数据进行平均值、中值、最常用值和常量值插补。
  + **azureml-contrib-pipeline-steps**
    + 接受要传递到 ParallelRunConfig 的计算名称字符串
  + **azureml-core**
    +  添加了 Environment.clone(new_name) API，用于创建 Environment 对象的副本
    +  Environment.docker.base_dockerfile 接受 filepath。 如果能够解析文件，则会将内容读取到 base_dockerfile 环境属性
    + 当用户在 Environment.docker 中手动设置值时，会自动为 base_image 和 base_dockerfile 重置互斥值
    + 在 RSection 中添加了 user_managed 标志，该标志指示环境由用户管理还是由 AzureML 管理。
    + 数据集：修复了数据路径包含 Unicode 字符时的数据集下载失败问题。
    + 数据集：改进了数据集装载缓存机制，以满足 Azure 机器学习计算中的最小磁盘空间需求，避免节点不可用以及作业被取消。
    + 数据集：当你访问 pandas 数据帧形式的时序数据集时，我们为时序列添加了一个索引，用于加快基于时序的数据访问速度。  以前，索引的名称与时间戳列的名称相同，用户难以区分哪个是实际的时间戳列，哪个是索引。 我们现在没有为索引指定任何特定名称，因为它不应当用作列。 
    + 数据集：修复了主权云中的数据集身份验证问题。
    + 数据集：修复了从 Azure PostgreSQL 数据存储创建的数据集的 `Dataset.to_spark_dataframe` 故障。
  + **azureml-interpret**
    + 当本地重要性值为稀疏值时，为可视化效果添加了全局分数
    + 已将 azureml-interpret 更新为使用 interpret-community 0.9.*
    + 修复了下载具有稀疏评估数据的解释时出现的问题
    + 在 AutoML 中添加了对解释对象的稀疏格式的支持
  + **azureml-pipeline-core**
    + 支持在管道中将 ComputeInstance 用作计算目标
  + **azureml-train-automl-client**
    + 围绕训练后操作添加了其他遥测。
    + 修复了提前停止的回归
    + 弃用了 azureml.dprep.Dataflow，它不再是输入数据的有效类型。
    +  将默认的 AutoML 试验超时值更改为六天。
  + **azureml-train-automl-runtime**
    + 围绕训练后操作添加了其他遥测。
    + 添加了稀疏 AutoML e2e 支持
  + **azureml-opendatasets**
    + 为服务监视器添加了其他遥测。
    + 为 blob 启用了 Front Door 以提高稳定性 

## <a name="2020-03-23"></a>2020-03-23

### <a name="azure-machine-learning-sdk-for-python-v120"></a>适用于 Python 的 Azure 机器学习 SDK v1.2.0

+ **重大更改**
  + 取消了对 Python 2.7 的支持

+ **Bug 修复与改进**
  + **azure-cli-ml**
    + 向 CLI 中的 `az ml model/computetarget/service` 命令添加了“--subscription-id”
    + 添加了以下支持：传递客户管理的密钥 (CMK) vault_url、key_name 和 key_version，用于 ACI 部署
  + **azureml-automl-core** 
    + 为 X 和 Y 数据预测任务启用了自定义插补和常量值。
    + 修复了向用户显示错误消息时的问题。    
  + **azureml-automl-runtime**
    + 修复了根据数据集（其中包含的粒度只有一行）进行预测时出现的问题
    + 降低了预测任务所需的内存量。
    + 添加了当时间列的格式不正确时显示的更好的错误消息。
    + 为 X 和 Y 数据预测任务启用了自定义插补和常量值。
  + **azureml-core**
    + 添加了对从以下环境变量加载 ServicePrincipal 的支持：AZUREML_SERVICE_PRINCIPAL_ID、AZUREML_SERVICE_PRINCIPAL_TENANT_ID 和 AZUREML_SERVICE_PRINCIPAL_PASSWORD
    + 为 `Dataset.Tabular.from_delimited_files` 引入了新参数 `support_multi_line`：默认情况下 (`support_multi_line=False`)，所有换行符（包括带引号的字段值中的那些）都将被解释为记录中断。 以这种方式读取数据更快并且更适合在多个 CPU 核心上并行执行。 但是，它可能会导致无声地生成更多字段值未对齐的记录。 在已知带分隔符的文件包含带引号的换行符的情况下，应将此项设置为 `True`。
    + 添加了在 Azure 机器学习 CLI 中注册 ADLS Gen2 的功能
    + 对于 TabularDataset 中的 with_timestamp_columns() 方法，已将参数“fine_grain_timestamp”重命名为“timestamp”，以及将参数“coarse_grain_timestamp”重命名为“partition_timestamp”，以更好地反映这些参数的用法。
    + 将最大试验名称长度增加到了 255。
  + **azureml-interpret**
    + 已将 azureml-interpret 更新为 interpret-community 0.7.*
  + **azureml-sdk**
    + 为支持预发行版和稳定发行版中的修补，此项将更改为具有兼容版本颚化符的依赖项。


## <a name="2020-03-11"></a>2020-03-11

### <a name="azure-machine-learning-sdk-for-python-v115"></a>适用于 Python 的 Azure 机器学习 SDK v1.1.5

+ **功能弃用**
  + **Python 2.7**
    + 最后一个支持 Python 2.7 的版本

+ **重大更改**
  + **语义化版本控制 2.0.0**
    + 从版本 1.1 开始，Azure ML Python SDK 采用语义化版本控制 2.0.0。 在[此处](https://semver.org/)了解详细信息。 所有后续版本将遵循新的编号方案和语义化版本控制协定。 

+ **Bug 修复与改进**
  + **azure-cli-ml**
    + 已将终结点 CLI 命令名称从“az ml endpoint aks”更改为“az ml endpoint real time”，以确保一致性。
    + 更新了有关稳定试验版分支 CLI 更新的 CLI 安装说明
    + 修复了单实例分析以生成建议，并在核心 SDK 中提供了此项修复。
  + **azureml-automl-core**
    + 为 AutoML ONNX 模型启用了批量模式推理（一次提取多行）
    + 改进了数据集频率检测，以及缺少数据或包含不规则数据点的问题
    + 添加了删除不符合主导频率的数据点的功能。
    + 更改了构造函数的输入，以提取选项列表来为相应的列应用插补选项。
    + 改进了错误日志记录。
  + **azureml-automl-runtime**
    + 修复了当训练集中不存在的粒度显示在测试集中时出现的问题和引发的错误
    + 删除了评分期间对预测服务施加的 y_query 要求
    + 修复了当数据集包含短粒度和长时隙时出现的预测问题。
    + 修复了在启用自动最大边际的情况下，日期列包含字符串格式的日期时出现的问题。 添加了适当的转换和错误消息来处理无法转换日期的问题
    + 使用本机 NumPy 和 SciPy 来序列化和反序列化 FileCacheStore 的中间数据（用于本地 AutoML 运行）
    + 修复了失败的子运行可能停滞于“正在运行”状态的 bug。
    + 提高了特征化的速度。
    + 修复了评分期间的频率检查。现在，在预测任务中，不再要求在训练集与测试集之间实施严格的频率均等性。
    + 更改了构造函数的输入，以提取选项列表来为相应的列应用插补选项。
    + 修复了与延隔类型选择相关的错误。
    + 修复了针对包含粒度和单行的数据集引发的未分类错误
    + 修复了频率检测速度缓慢的问题。
    + 修复了 AutoML 异常处理中的一个 bug，该 bug 会导致 AttributeError 取代训练失败的实际原因。
  + **azureml-cli-common**
    + 修复了单实例分析以生成建议，并在核心 SDK 中提供了此项修复。
  + **azureml-contrib-mir**
    + 在 MirWebservice 类中添加了用于检索访问令牌的功能
    + 在调用 MirWebservice.run() 期间，默认将对 MirWebservice 使用令牌身份验证 - 仅当调用失败时才刷新
    + MirWebservice 部署现在要求提供适当的 SKU [Standard_DS2_v2, Standard_F16, Standard_A2_v2]，而不是对应的 [Ds2v2, A2v2, F16]。
  + **azureml-contrib-pipeline-steps**
    + 已将可选参数 side_inputs 添加到 ParallelRunStep。 此参数可用于在容器中装载文件夹。 当前支持的类型为 DataReference 和 PipelineData。
    + 现在，可以通过传递管道参数来覆盖 ParallelRunConfig 中传入的参数。 支持新的管道参数 aml_mini_batch_size、aml_error_threshold、aml_logging_level、aml_run_invocation_timeout（aml_node_count 和 aml_process_count_per_node 已包含在早期版本中）。
  + **azureml-core**
    + 部署的 AzureML Web 服务现在默认使用 `INFO` 日志记录。 可以通过在部署的服务中设置 `AZUREML_LOG_LEVEL` 环境变量来控制此选项。
    + Python SDK 通过发现服务来使用“api”终结点而不是“pipelines”。
    + 交换到所有 SDK 调用中的新路由。
    + 已将 ModelManagementService 的调用路由更改为新的统一结构。
      + 公开发布了工作区更新方法。
      + 在工作区更新方法中添加了 image_build_compute 参数，使用户能够更新映像生成的计算。
    + 将弃用消息添加到了旧分析工作流。 修复了分析 CPU 和内存限制。
    + 已将 RSection 添加为 Environment 的一部分，以运行 R 作业。
    + 在 `Dataset.mount` 中添加了验证，以便在数据集源不可访问或不包含任何数据时引发错误。
    + 添加了 `--grant-workspace-msi-access` 作为数据存储 CLI 的附加参数，用于注册 Azure Blob 容器，使你能够注册位于 VNet 后面的 Blob 容器。
    + 修复了单实例分析以生成建议，并在核心 SDK 中提供了此项修复。
    + 修复了 aks.py _deploy 中的问题。
    + 将验证要上传的模型的完整性，以避免静默式存储失败。
    + 现在，用户在为 Web 服务重新生成密钥时可为身份验证密钥指定值。
    + 修复了不能将大写字母用作数据集输入名称的 bug。
  + **azureml-defaults**
    + `azureml-dataprep` 现在将作为 `azureml-defaults` 的一部分安装。 不再需要在计算目标上手动安装 data prep[fuse] 即可装载数据集。
  + **azureml-interpret**
    + 已将 azureml-interpret 更新为 interpret-community 0.6.*
    + 更新了 azureml-interpret，使其依赖于 interpret-community 0.5.0
    + 添加了 azureml-interpret 的 azureml-style 异常
    + 修复了 keras 模型的 DeepScoringExplainer 序列化
  + **azureml-mlflow**
    + 为 azureml.mlflow 添加了主权云支持。
  + **azureml-pipeline-core**
    + 管道批量评分笔记本现在使用 ParallelRunStep
    + 修复了以下 bug：即使更改参数列表，也可能会错误地重复使用 PythonScriptStep 结果
    + 添加了在 `PipelineOutputFileDataset` 中调用 parse_* 方法时设置列类型的功能
  + **azureml-pipeline-steps**
    + 已将 `AutoMLStep` 移到 `azureml-pipeline-steps` 包。 弃用了 `azureml-train-automl-runtime` 中的 `AutoMLStep`。
    + 添加了将数据集用作 PythonScriptStep 输入的文档示例
  + **azureml-tensorboard**
    + 更新了 azureml-tensorboard 以支持 tensorflow 2.0
    + 在计算实例上使用自定义 Tensorboard 端口时显示正确的端口号
  + **azureml-train-automl-client**
    + 修复了可能在错误的远程运行版本中安装某些包的问题。
    + 修复了筛选自定义特征化配置的 FeaturizationConfig 重写问题。
  + **azureml-train-automl-runtime**
    + 修复了远程运行中的频率检测问题
    + 移动了 `azureml-pipeline-steps` 包中的 `AutoMLStep`。 弃用了 `azureml-train-automl-runtime` 中的 `AutoMLStep`。
  + **azureml-train-core**
    + 在 PyTorch 估算器中支持 PyTorch 版本 1.4
  
## <a name="2020-03-02"></a>2020-03-02

### <a name="azure-machine-learning-sdk-for-python-v112rc0-pre-release"></a>适用于 Python 的 Azure 机器学习 SDK v1.1.2rc0（预发行版）

+ **Bug 修复与改进**
  + **azureml-automl-core**
    + 为 AutoML ONNX 模型启用了批量模式推理（一次提取多行）
    + 改进了数据集频率检测，以及缺少数据或包含不规则数据点的问题
    + 添加了删除不符合主导频率的数据点的功能。
  + **azureml-automl-runtime**
    + 修复了当训练集中不存在的粒度显示在测试集中时出现的问题和引发的错误
    + 删除了评分期间对预测服务施加的 y_query 要求
  + **azureml-contrib-mir**
    + 在 MirWebservice 类中添加了用于检索访问令牌的功能
  + **azureml-core**
    + 部署的 AzureML Web 服务现在默认使用 `INFO` 日志记录。 可以通过在部署的服务中设置 `AZUREML_LOG_LEVEL` 环境变量来控制此选项。
    + 修复了 `Dataset.get_all` 迭代，以返回已注册到工作区的所有数据集。
    + 改进了向数据集创建 API 的 `path` 参数传递无效类型时显示的错误消息。
    + Python SDK 通过发现服务来使用“api”终结点而不是“pipelines”。
    + 交换到所有 SDK 调用中的新路由
    + 将 ModelManagementService 的调用路由更改为新的统一结构
      + 公开发布了工作区更新方法。
      + 在工作区更新方法中添加了 image_build_compute 参数，使用户能够更新映像生成的计算
    +  将弃用消息添加到了旧分析工作流。 修复了分析 CPU 和内存限制
  + **azureml-interpret**
    + 已将 azureml-interpret 更新为 interpret-community 0.6.*
  + **azureml-mlflow**
    + 为 azureml.mlflow 添加了主权云支持。
  + **azureml-pipeline-steps**
    + 已将 `AutoMLStep` 移到 `azureml-pipeline-steps package`。 弃用了 `azureml-train-automl-runtime` 中的 `AutoMLStep`。
  + **azureml-train-automl-client**
    + 修复了可能在错误的远程运行版本中安装某些包的问题。
  + **azureml-train-automl-runtime**
    + 修复了远程运行中的频率检测问题
    + 已将 `AutoMLStep` 移到 `azureml-pipeline-steps package`。 弃用了 `azureml-train-automl-runtime` 中的 `AutoMLStep`。
  + **azureml-train-core**
    + 已将 `AutoMLStep` 移到 `azureml-pipeline-steps package`。 弃用了 `azureml-train-automl-runtime` 中的 `AutoMLStep`。

## <a name="2020-02-18"></a>2020-02-18

### <a name="azure-machine-learning-sdk-for-python-v111rc0-pre-release"></a>适用于 Python 的 Azure 机器学习 SDK v1.1.1rc0（预发行版）

+ **Bug 修复与改进**
  + **azure-cli-ml**
    + 修复了单实例分析以生成建议，并在核心 SDK 中提供了此项修复。
  + **azureml-automl-core**
    + 改进了错误日志记录。
  + **azureml-automl-runtime**
    + 修复了当数据集包含短粒度和长时隙时出现的预测问题。
    + 修复了在启用自动最大边际的情况下，日期列包含字符串格式的日期时出现的问题。 添加了适当的转换和解释性的错误用于解决无法进行日期转换的问题
    + 使用本机 NumPy 和 SciPy 来序列化和反序列化 FileCacheStore 的中间数据（用于本地 AutoML 运行）
    + 修复了失败的子运行可能停滞于“正在运行”状态的 bug。
  + **azureml-cli-common**
    + 修复了单实例分析以生成建议，并在核心 SDK 中提供了此项修复。
  + **azureml-core**
    + 添加了 `--grant-workspace-msi-access` 作为数据存储 CLI 的附加参数，用于注册 Azure Blob 容器，使你能够注册位于 VNet 后面的 Blob 容器
    + 修复了单实例分析以生成建议，并在核心 SDK 中提供了此项修复。
    + 修复了 aks.py _deploy 中的问题
    + 将验证要上传的模型的完整性，以避免静默式存储失败。
  + **azureml-interpret**
    + 添加了 azureml-interpret 的 azureml-style 异常
    + 修复了 keras 模型的 DeepScoringExplainer 序列化
  + **azureml-pipeline-core**
    + 管道批量评分笔记本现在使用 ParallelRunStep
  + **azureml-pipeline-steps**
    + 移动了 `azureml-pipeline-steps` 包中的 `AutoMLStep`。 弃用了 `azureml-train-automl-runtime` 中的 `AutoMLStep`。
  + **azureml-contrib-pipeline-steps**
    + 已将可选参数 side_inputs 添加到 ParallelRunStep。 此参数可用于在容器中装载文件夹。 当前支持的类型为 DataReference 和 PipelineData。
  + **azureml-tensorboard**
    + 更新了 azureml-tensorboard 以支持 tensorflow 2.0
  + **azureml-train-automl-client**
    + 修复了筛选自定义特征化配置的 FeaturizationConfig 重写问题。
  + **azureml-train-automl-runtime**
    + 移动了 `azureml-pipeline-steps` 包中的 `AutoMLStep`。 弃用了 `azureml-train-automl-runtime` 中的 `AutoMLStep`。
  + **azureml-train-core**
    + 在 PyTorch 估算器中支持 PyTorch 版本 1.4
  
## <a name="2020-02-04"></a>2020-02-04

### <a name="azure-machine-learning-sdk-for-python-v110rc0-pre-release"></a>适用于 Python 的 Azure 机器学习 SDK v1.1.0rc0（预发行版）

+ **重大更改**
  + **语义化版本控制 2.0.0**
    + 从版本 1.1 开始，Azure ML Python SDK 采用语义化版本控制 2.0.0。 在[此处](https://semver.org/)了解详细信息。 所有后续版本将遵循新的编号方案和语义化版本控制协定。 
  
+ **Bug 修复与改进**
  + **azureml-automl-runtime**
    + 提高了特征化的速度。
    + 修复了评分期间的频率检查。现在，在预测任务中，我们不要求在训练集与测试集之间实施严格的频率均等性。
  + **azureml-core**
    + 现在，用户在为 Web 服务重新生成密钥时可为身份验证密钥指定值。
  + **azureml-interpret**
    + 更新了 azureml-interpret，使其依赖于 interpret-community 0.5.0
  + **azureml-pipeline-core**
    + 修复了以下 bug：即使更改参数列表，也可能会错误地重复使用 PythonScriptStep 结果
  + **azureml-pipeline-steps**
    + 添加了将数据集用作 PythonScriptStep 输入的文档示例
  + **azureml-contrib-pipeline-steps**
    + 现在，可以通过传递管道参数来覆盖 ParallelRunConfig 中传入的参数。 支持新的管道参数 aml_mini_batch_size、aml_error_threshold、aml_logging_level、aml_run_invocation_timeout（aml_node_count 和 aml_process_count_per_node 已包含在早期版本中）。
  
## <a name="2020-01-21"></a>2020-01-21

### <a name="azure-machine-learning-sdk-for-python-v1085"></a>适用于 Python 的 Azure 机器学习 SDK v1.0.85

+ **新功能**
  + **azureml-core**
    + 获取给定工作区和订阅中 AmlCompute 资源的当前核心用量和配额限制
  
  + **azureml-contrib-pipeline-steps**
    + 使用户能够将上一步骤中的表格数据集作为中间结果传递给 parallelrunstep

+ **Bug 修复与改进**
  + **azureml-automl-runtime**
    + 取消了在向部署的预测服务发出的请求必须提供 y_query 列的要求。 
    + “y_query”已从 Dominick Orange Juice 笔记本服务请求节中删除。
    + 修复了阻止在部署的模型中进行预测，以及对包含日期时间列的数据集进行操作的 bug。
    + 添加了 Matthews 相关系数作为分类指标，用于二元和多类分类。
  + **azureml-contrib-interpret**
    + 从 azureml-contrib-interpret 中删除了文本解释器，因为文本解释已转移到即将发布的 interpret-text 存储库。
  + **azureml-core**
    + 数据集：文件数据集的用法不再依赖于 numpy 和 pandas 即可安装在 python 环境中。
    + 更改了 LocalWebservice.wait_for_deployment()，以便先检查本地 Docker 容器的状态，然后再尝试 ping 其运行状况终结点，这大幅减少了报告失败部署所需的时间。
    + 修复了在使用 LocalWebservice() 构造函数从现有部署创建服务对象时，在 LocalWebservice.reload() 中使用的内部属性的初始化。
    + 编辑了错误消息以澄清问题。
    + 将名为 get_access_token() 的新方法添加到了 AksWebservice，该方法返回 AksServiceAccessToken 对象，而该对象包含访问令牌、刷新截止时间戳、时间戳中的过期时间，以及令牌类型。 
    + AksWebservice 中已弃用现有的 get_token() 方法，因为此方法返回的所有信息可由新方法返回。
    + 修改了 az ml service get-access-token 命令的输出。 已将 token 重命名为 accessToken，已将 refreshBy 重命名为 refreshAfter。 添加了 expiryOn 和 tokenType 属性。
    + 修复了 get_active_runs
  + **azureml-explain-model**
    + 已将 shap 更新为 0.33.0，已将 interpret-community 更新为 0.4.*
  + **azureml-interpret**
    + 已将 shap 更新为 0.33.0，已将 interpret-community 更新为 0.4.*
  + **azureml-train-automl-runtime**
    + 添加了 Matthews 相关系数作为分类指标，用于二元和多类分类。
    + 弃用了在代码中的预处理标志，并已将其替换为特征化 - 特征化默认已启用

## <a name="2020-01-06"></a>2020-01-06

### <a name="azure-machine-learning-sdk-for-python-v1083"></a>适用于 Python 的 Azure 机器学习 SDK v1.0.83

+ **新功能**
  + 数据集：添加了两个选项 `on_error` 和 `out_of_range_datetime`，以便在数据包含错误值时使 `to_pandas_dataframe` 失败，而不是用 `None` 来填充它们。
  + 工作区：为包含敏感数据的工作区添加了 `hbi_workspace` 标志，以便启用进一步的加密，并对工作区禁用高级诊断。 我们还添加了为关联的 Cosmos DB 实例使用自己的密钥的支持，若要获得此项支持，可以在创建工作区时指定 `cmk_keyvault` 和 `resource_cmk_uri` 参数，以便在预配工作区时在订阅中创建 Cosmos DB 实例。 [在此处了解详细信息。](https://docs.microsoft.com/azure/machine-learning/concept-enterprise-security#azure-cosmos-db)

+ **Bug 修复与改进**
  + **azureml-automl-runtime**
    + 修复了在低于 3.5.4 的 Python 版中运行 AutoML 时导致引发 TypeError 的回归问题。
  + **azureml-core**
    + 修复了 `datastore.upload_files` 中的 bug：无法使用不是以 `./` 开头的相对路径。
    + 针对所有 Image 类代码路径添加了弃用消息
    + 修复了 Azure 中国世纪互联区域的模型管理 URL 构造。
    + 修复了以下问题：无法为 Azure Functions 打包使用 source_dir 的模型。    
    + 将一个选项添加到了 [Environment.build_local()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)，以将映像推送到 AzureML 工作区容器注册表中
    + 更新了 SDK，以通过后向兼容的方式在 Azure Synapse 中使用新的令牌库。
  + **azureml-interpret**
    + 修复了以下 bug：未提供下载说明时返回 None。 现在会引发异常，与其他位置的行为相匹配。
  + **azureml-pipeline-steps**
    + 在 `EstimatorStep` 中使用 `Estimator` 时，禁止将 `DatasetConsumptionConfig`s 传递给 `Estimator` 的 `inputs` 参数。
  + **azureml-sdk**
    + 将 AutoML 客户端添加到了 azureml-sdk 包，以便可以在不安装完整 AutoML 包的情况下提交远程 AutoML 运行。
  + **azureml-train-automl-client**
    + 更正了 AutoML 运行的控制台输出对齐方式
    + 修复了以下 bug：可能在远程 amlcompute 上安装错误的 pandas 版本。

## <a name="2019-12-23"></a>2019-12-23

### <a name="azure-machine-learning-sdk-for-python-v1081"></a>适用于 Python 的 Azure 机器学习 SDK v1.0.81

+ **Bug 修复与改进**
  + **azureml-contrib-interpret**
    + 将 shap 依赖项从 azureml-interpret 推迟到 interpret-community
  + **azureml-core**
    + 现在可将计算目标指定为相应部署配置对象的参数。 具体而言，这是要部署到的计算目标的名称，而不是 SDK 对象的名称。
    + 已将 CreatedBy 信息添加到 Model 和 Service 对象。 可以通过 <var>.created_by 访问
    + 修复了无法正确设置 Docker 容器 HTTP 端口的 ContainerImage.run()。
    + 使 `azureml-dataprep` 成为 CLI 命令 `az ml dataset register` 的可选参数
    + 修复了以下 bug：`TabularDataset.to_pandas_dataframe` 错误地回退到备用读取器并输出警告。
  + **azureml-explain-model**
    + 将 shap 依赖项从 azureml-interpret 推迟到 interpret-community
  + **azureml-pipeline-core**
    + 添加了新的管道步骤 `NotebookRunnerStep`，用于将本地笔记本作为管道中的步骤运行。
    + 删除了 PublishedPipelines、Schedules 和 PipelineEndpoints 的已弃用 get_all 函数
  + **azureml-train-automl-client**
    + 已开始弃用用作 AutoML 的输入的 data_script。


## <a name="2019-12-09"></a>2019-12-09

### <a name="azure-machine-learning-sdk-for-python-v1079"></a>适用于 Python 的 Azure 机器学习 SDK v1.0.79

+ **Bug 修复与改进**
  + **azureml-automl-core**
    + 删除了要记录的 featurizationConfig
      + 已更新为仅记录到日志“auto”/“off”/“customized”。
  + **azureml-automl-runtime**
    + 添加了对 pandas 的支持。 Series 和 pandas。 用于检测列数据类型的分类。 以前仅支持 numpy.ndarray
      + 添加了相关代码更改用于正确处理分类 dtype。
    + 改进了预测函数接口：y_pred 参数是可选的。 \- 改进了 docstrings。
  + **azureml-contrib-dataset**
    + 修复了无法装载已标记数据集的 bug。
  + **azureml-core**
    + `Environment.from_existing_conda_environment(name, conda_environment_name)` 的 Bug 修复。 用户可以创建用作本地环境的确切副本的 Environment 实例
    + 默认将时序相关的 Datasets 方法更改为 `include_boundary=True`。
  + **azureml-train-automl-client**
    + 修复了 show output 设置为 false 时不输出验证结果的问题。


## <a name="2019-11-25"></a>2019-11-25

### <a name="azure-machine-learning-sdk-for-python-v1076"></a>适用于 Python 的 Azure 机器学习 SDK v1.0.76

+ **重大更改**
  + Azureml-Train-AutoML 升级问题
    + 从 azureml-train-automl<1.0.76 升级到 azureml-train-automl>=1.0.76 可能导致安装不完整，从而导致某些 AutoML 导入失败。 若要解决此问题，可以运行 https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/automl_setup.cmd 中的安装脚本。 如果直接使用 pip，则可以运行：
      + "pip install --upgrade azureml-train-automl"
      + "pip install --ignore-installed azureml-train-automl-client"
    + 或者，可以在升级之前卸载旧版本
      + "pip uninstall azureml-train-automl"
      + "pip install azureml-train-automl"

+ **Bug 修复与改进**
  + **azureml-automl-runtime**
    + 在计算二元分类任务的平均标量指标时，AutoML 现在会考虑 true 类和 false 类。
    + 已将 AzureML-AutoML-Core 中的机器学习和训练代码移到新包 AzureML-AutoML-Runtime。
  + **azureml-contrib-dataset**
    + 使用 download 选项针对标记的数据集调用 `to_pandas_dataframe` 时，现在可以指定是否覆盖现有文件。
    + 调用导致删除 time series、label 或 image 列的 `keep_columns` 或 `drop_columns` 时，也会删除数据集的相应功能。
    + 修复了对象检测任务的 pytorch 加载程序存在的问题。
  + **azureml-contrib-interpret**
    + 从 azureml-contrib-interpret 中删除了解释仪表板小组件，并已将包更改为引用 interpret_community 中新的小组件
    + 已将 interpret-community 的版本更新为 0.2.0
  + **azureml-core**
    + 改进了 `workspace.datasets` 的性能。
    + 添加了使用用户名和密码身份验证注册 Azure SQL 数据库数据存储的功能
    + 修复了从相对路径加载 RunConfigurations 的问题。
    + 调用导致删除 time series 列的 `keep_columns` 或 `drop_columns` 时，也会删除数据集的相应功能。
  + **azureml-interpret**
    + 已将 interpret-community 的版本更新为 0.2.0
  + **azureml-pipeline-steps**
    + 阐述了 Azure 机器学习管道步骤支持的 `runconfig_pipeline_params` 值。
  + **azureml-pipeline-core**
    + 为 Pipeline 命令添加了用于下载 JSON 格式的输出的 CLI 选项。
  + **azureml-train-automl**
    + 已将 AzureML-Train-AutoML 拆分为两个程序包：一个客户端程序包 AzureML-Train-AutoML-Client，一个机器学习训练程序包 AzureML-Train-AutoML-Runtime
  + **azureml-train-automl-client**
    + 添加了一个瘦客户端用于提交 AutoML 试验，而无需在本地安装任何机器学习依赖项。
    + 修复了在远程运行中记录自动检测到的延迟、滚动窗口大小和最大边际的问题。
  + **azureml-train-automl-runtime**
    + 添加了新的 AutoML 包，用于从客户端隔离机器学习和运行时组件。
  + **azureml-contrib-train-rl**
    + 在 SDK 中添加了强化学习支持。
    + 在 RL SDK 中添加了 AmlWindowsCompute 支持。


## <a name="2019-11-11"></a>2019-11-11

### <a name="azure-machine-learning-sdk-for-python-v1074"></a>适用于 Python 的 Azure 机器学习 SDK v1.0.74

  + **预览功能**
    + **azureml-contrib-dataset**
      + 导入 azureml-contrib-dataset 后，可以调用 `Dataset.Labeled.from_json_lines`（而不是 `._Labeled`）来创建标记的数据集。
      + 使用 download 选项针对标记的数据集调用 `to_pandas_dataframe` 时，现在可以指定是否覆盖现有文件。
      + 调用导致删除 time series、label 或 image 列的 `keep_columns` 或 `drop_columns` 时，也会删除数据集的相应功能。
      + 修复了调用 `dataset.to_torchvision()` 时 PyTorch 加载程序存在的问题。

+ **Bug 修复与改进**
  + **azure-cli-ml**
    + 已将模型分析添加到预览版 CLI。
    + 修复了 Azure 存储中导致 AzureML CLI 失败的中断性变更。
    + 已将负载均衡器类型添加到 AKS 类型的 MLC
  + **azureml-automl-core**
    + 修复了检测时序中的最大边际时缺少值和出现多个粒度的问题。
    + 修复了生成交叉验证拆分过程中发生失败的问题。
    + 将本部分替换为要显示在发行说明中的 markdown 格式的消息：- 改进了处理预测数据集中短粒度的方法。
    + 修复了日志记录期间将某些用户信息掩码的问题。 \- 改进了预测运行期间记录错误的方法。
    + 将 psutil 作为 conda 依赖项添加到自动生成的 yml 部署文件中。
  + **azureml-contrib-mir**
    + 修复了 Azure 存储中导致 AzureML CLI 失败的中断性变更。
  + **azureml-core**
    + 修复了导致 Azure Functions 上部署的模型生成 500 错误的 bug。
    + 修复了 amlignore 文件不应用于快照的问题。
    + 添加了新的 API amlcompute.get_active_runs，用于返回要运行的生成器，并使运行在给定的 amlcompute 上排队。
    + 已将负载均衡器类型添加到 AKS 类型的 MLC。
    + 已将 append_prefix bool 参数添加到 run.py 中的 download_files，以及 download_artifacts_from_prefix 中的 artifacts_client。 此标志用于选择性地平展原始文件路径，以便只将文件或文件夹名称添加到 output_directory
    + 修复了包含数据集用法的 `run_config.yml` 的反序列化问题。
    + 调用导致删除 time series 列的 `keep_columns` 或 `drop_columns` 时，也会删除数据集的相应功能。
  + **azureml-interpret**
    + 已将 interpret-community 版本更新为 0.1.0.3
  + **azureml-train-automl**
    + 修复了 automl_step 可能无法输出验证问题的问题。
    + 修复了即使模型的环境在本地缺少依赖项，register_model 也能成功的问题。
    + 修复了不为某些远程运行启用 docker 的问题。
    + 添加了导致本地运行提前失败的异常的日志记录。
  + **azureml-train-core**
    + 在自动超参数优化最佳子运行的计算中考虑 resume_from 运行。
  + **azureml-pipeline-core**
    + 修复了管道自变量构造中的参数处理。
    + 添加了管道说明和步骤类型 yaml 参数。
    + 为管道步骤添加了新的 yaml 格式，并针对旧格式添加了弃用警告。



## <a name="2019-11-04"></a>2019-11-04

### <a name="web-experience"></a>Web 体验

位于 [https://ml.azure.com](https://ml.azure.com) 的协作工作区登陆页已增强，已更名为“Azure 机器学习工作室（预览版）”。

在工作室中，可以训练、测试、部署和管理数据集、管道、模型、终结点等 Azure 机器学习资产。

从工作室访问以下基于 Web 的创作工具：

| 基于 Web 的工具 | 说明 | 版本 |
|-|-|-|
| 笔记本 VM（预览版） | 完全托管的基于云的工作站 | 基本和企业版 |
| [自动化机器学习](tutorial-first-experiment-automated-ml.md)（预览版） | 没有可用于自动化机器学习模型开发的代码体验 | Enterprise |
| [设计器](concept-designer.md)（预览版） | 以前称为“设计器”的拖放式机器学习建模工具 | Enterprise |


### <a name="azure-machine-learning-designer-enhancements"></a>Azure 机器学习设计器增强

+ 前称为“可视化界面” 
+    11 个新[模块](algorithm-module-reference/module-reference.md)，包括推荐器、分类器和训练实用工具，例如特征工程、交叉验证和数据转换。

### <a name="r-sdk"></a>R SDK 
 
数据科学家和 AI 开发人员可以结合 Azure 机器学习使用[适用于 R 的 Azure 机器学习 SDK](tutorial-1st-r-experiment.md) 来构建和运行机器学习工作流。

适用于 R 的 Azure 机器学习 SDK 使用 `reticulate` 包绑定到 Python SDK。 直接绑定到 Python 后，可以通过适用于 R 的 SDK 从所选的任何 R 环境访问 Python SDK 中实现的核心对象和方法。

SDK 的主要功能包括：

+    管理用于监视、记录和组织机器学习试验的云资源。
+    使用云资源训练模型，包括 GPU 加速的模型训练。
+    在 Azure 容器实例 (ACI) 和 Azure Kubernetes 服务 (AKS) 中将模型部署为 Web 服务。

有关完整文档，请参阅[包网站](https://azure.github.io/azureml-sdk-for-r)。

### <a name="azure-machine-learning-integration-with-event-grid"></a>Azure 机器学习与事件网格的集成 

Azure 机器学习现在是事件网格的资源提供程序，你可以通过 Azure 门户或 Azure CLI 配置机器学习事件。 用户可以针对运行完成状态、模型注册、模型部署以及检测到的数据偏移创建事件。 可将这些事件路由到事件网格支持的事件处理程序以供使用。 有关更多详细信息，请参阅机器学习事件[架构](https://docs.microsoft.com/azure/event-grid/event-schema-machine-learning)和[教程](how-to-use-event-grid.md)文章。

## <a name="2019-10-31"></a>2019-10-31

### <a name="azure-machine-learning-sdk-for-python-v1072"></a>适用于 Python 的 Azure 机器学习 SDK v1.0.72

+ **新功能**
  + 通过 [**azureml-datadrift**](https://docs.microsoft.com/python/api/azureml-datadrift) 包添加了数据集监视器，用于持续监视存在数据偏移的时序数据集或其他统计更改。 如果检测到偏移或符合针对数据的其他条件，则可以触发警报和事件。 有关详细信息，请参阅[我们的文档](how-to-monitor-datasets.md)。
  + 在 Azure 机器学习中宣布两个新版本（也称为 SKU）。 在此版本中，现在可以创建“基本”或“企业”Azure 机器学习工作区。 所有现有工作区默认为“基本”版本，随时可以转到 Azure 门户或工作室来升级工作区。 可以从 Azure 门户创建“基本”或“企业”工作区。 有关详细信息，请阅读[我们的文档](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace)。 在 SDK 中，可以使用工作区对象的“sku”属性确定工作区的版本。
  + 我们还增强了 Azure 机器学习计算 - 现在，可以在 Azure Monitor 中查看群集的指标（例如节点总数、正在运行的节点、总核心配额），并可以查看用于调试的诊断日志。 此外，还可以查看群集上当前正在运行或已排队的运行，以及群集中各个节点的 IP 等详细信息。 可以在门户中查看这些信息，或者使用 SDK 或 CLI 中的相应函数来查看。

  + **预览功能**
    + 我们正在发布 Azure 机器学习计算中本地 SSD 磁盘加密的预览版支持。 请提供技术支持票证，使订阅允许列表使用此功能。
    + Azure 机器学习批量推理公共预览版。 Azure 机器学习批量推理面向非时间敏感型的大型推理作业。 批量推理提供经济高效的推理计算缩放功能，并为异步应用程序提供无可比拟的吞吐量。 它已针对高吞吐量进行优化，可针对大型数据集合实现即发即弃的推理。
    + [**azureml-contrib-dataset**](https://docs.microsoft.com/python/api/azureml-contrib-dataset)
        + 已实现标记数据集的功能
        ```Python
        import azureml.core
        from azureml.core import Workspace, Datastore, Dataset
        import azureml.contrib.dataset
        from azureml.contrib.dataset import FileHandlingOption, LabeledDatasetTask

        # create a labeled dataset by passing in your JSON lines file
        dataset = Dataset._Labeled.from_json_lines(datastore.path('path/to/file.jsonl'), LabeledDatasetTask.IMAGE_CLASSIFICATION)

        # download or mount the files in the `image_url` column
        dataset.download()
        dataset.mount()

        # get a pandas dataframe
        from azureml.data.dataset_type_definitions import FileHandlingOption
        dataset.to_pandas_dataframe(FileHandlingOption.DOWNLOAD)
        dataset.to_pandas_dataframe(FileHandlingOption.MOUNT)

        # get a Torchvision dataset
        dataset.to_torchvision()
        ```

+ **Bug 修复与改进**
  + **azure-cli-ml**
    + CLI 现在支持模型打包。
    + 添加了数据集 CLI。 详细信息：`az ml dataset --help`
    + 添加了对部署和打包受支持模型（ONNX、scikit-learn 和 TensorFlow）的支持，无需 InferenceConfig 实例。
    + 在 SDK 和 CLI 中为服务部署（ACI 和 AKS）添加了覆盖标志。 如果提供此标志，将覆盖现有服务（如果存在同名的服务）。 如果服务不存在，将创建新服务。
    + 可将模型注册到 Onnx 和 Tensorflow 这两个新框架。 - 模型注册接受模型的示例输入数据、示例输出数据和资源配置。
  + **azureml-automl-core**
    + 仅当设置运行时约束时，才会在子进程中运行迭代。
    + 为预测任务添加了准则，用于检查指定的 max_horizon 是否导致给定的计算机上出现内存问题。 如果是，将显示一条准则消息。
    + 添加了对复杂频率（例如两年和一个月）的支持。 \- 添加了无法确定频率时显示的易于理解的错误消息。
    + 在自动生成的 conda env 中添加了 azureml-defaults 用于解决模型部署失败
    + 允许将 Azure 机器学习管道中的中间数据转换为表格数据集，并在 `AutoMLStep` 中使用。
    + 为流式处理实现了列用途更新。
    + 为用于流式处理的 Imputer 和 HashOneHotEncoder 实现了转换器参数更新。
    + 已将当前数据大小和所需最小数据大小添加到了验证错误消息中。
    + 更新了交叉验证所需的最小数据大小，以保证每个验证折叠中至少有两个样本。
  + **azureml-cli-common**
    + CLI 现在支持模型打包。
    + 可将模型注册到 Onnx 和 Tensorflow 这两个新框架。
    + 模型注册接受模型的示例输入数据、示例输出数据和资源配置。
  + **azureml-contrib-gbdt**
    + 修复了笔记本的发布通道
    + 为不支持的非 AmlCompute 计算目标添加了警告
    + 已将 LightGMB 评估器添加到 azureml-contrib-gbdt 包
  + [**azureml-core**](https://docs.microsoft.com/python/api/azureml-core)
    + CLI 现在支持模型打包。
    + 为已弃用的数据集 API 添加了弃用警告。 请参阅 https://aka.ms/tabular-dataset 上的数据集 API 更改通知。
    + 更改了 [`Dataset.get_by_id`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29#get-by-id-workspace--id-)，以便在注册数据集后返回注册名称和版本。
    + 修复了无法反复使用将数据集用作参数的 ScriptRunConfig 来提交试验运行的 bug。
    + 在运行期间检索到的数据集将受到跟踪，并可以在运行详细信息页中查看，或者在运行完成后调用 [`run.get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29#get-details--) 来查看。
    + 允许将 Azure 机器学习管道中的中间数据转换为表格数据集，并在 [`AutoMLStep`](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlstep) 中使用。
    + 添加了对部署和打包受支持模型（ONNX、scikit-learn 和 TensorFlow）的支持，无需 InferenceConfig 实例。
    + 在 SDK 和 CLI 中为服务部署（ACI 和 AKS）添加了覆盖标志。 如果提供此标志，将覆盖现有服务（如果存在同名的服务）。 如果服务不存在，将创建新服务。
    +  可将模型注册到 Onnx 和 Tensorflow 这两个新框架。 模型注册接受模型的示例输入数据、示例输出数据和资源配置。
    + 为 Azure Database for MySQL 添加了新的数据存储。 添加了有关在 Azure 机器学习管道的 DataTransferStep 中使用 Azure Database for MySQL 的示例。
    + 添加了在试验中添加和删除标记的功能；添加了从运行中删除标记的功能
    + 在 SDK 和 CLI 中为服务部署（ACI 和 AKS）添加了覆盖标志。 如果提供此标志，将覆盖现有服务（如果存在同名的服务）。 如果服务不存在，将创建新服务。
  + [**azureml-datadrift**](https://docs.microsoft.com/python/api/azureml-datadrift)
    + 已从 `azureml-contrib-datadrift` 移入 `azureml-datadrift`
    + 添加了监视时序数据集的偏移和其他统计度量的支持
    + 已将新方法 `create_from_model()` 和 `create_from_dataset()` 添加到 [`DataDriftDetector`](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector(class)) 类。 `create()` 方法即将弃用。
    + 对 Azure 机器学习工作室中的 Python 和 UI 中的可视化效果做了调整。
    + 数据集监视器除了每日监视计划以外，还支持每周和每月监视计划。
    + 支持数据监视指标的回填，以分析数据集监视器的历史数据。
    + 各种 Bug 修复
  + [**azureml-pipeline-core**](https://docs.microsoft.com/python/api/azureml-pipeline-core)
    + 从管道 `yaml` 文件提交 Azure 机器学习管道运行不再需要 azureml-dataprep。
  + [**azureml-train-automl**](/python/api/azureml-train-automl-runtime/)
    + 在自动生成的 conda env 中添加了 azureml-defaults 用于解决模型部署失败
    + AutoML 远程训练现在包含 azureml-defaults，允许重复使用训练 env 进行推理。
  + **azureml-train-core**
    + 在 [`PyTorch`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch) 评估器中添加了 PyTorch 1.3 支持

## <a name="2019-10-21"></a>2019-10-21

### <a name="visual-interface-preview"></a>可视化界面（预览版）

+ Azure 机器学习可视化界面（预览版）经过大幅修改，可在 [Azure 机器学习管道](concept-ml-pipelines.md)中运行。 在可视化界面中创作的管道（前称为“试验”）现在与核心 Azure 机器学习体验完全集成。
  + SDK 资产的统一管理体验
  + 可视化界面模型、管道和终结点的版本控制与跟踪
  + 重新设计了 UI
  + 添加了批量推理部署
  + 为推理计算目标添加了 Azure Kubernetes 服务 (AKS) 支持
  + 新的 Python 步骤管道创作工作流
  + 视觉创作工具的新[登陆页](https://ml.azure.com)

+ **新模块**
  + 应用数学运算
  + 应用 SQL 转换
  + 剪切值
  + 汇总数据
  + 从 SQL 数据库导入

## <a name="2019-10-14"></a>2019-10-14

### <a name="azure-machine-learning-sdk-for-python-v1069"></a>适用于 Python 的 Azure 机器学习 SDK v1.0.69

+ **Bug 修复与改进**
  + **azureml-automl-core**
    + 将模型解释限制为最佳运行，而不是计算每个运行的解释。 即将对本地、远程和 ADB 资源做出此项行为更改。
    + 添加了对 UI 的按需模型解释的支持
    + 添加了 psutil 作为 `automl` 的依赖项，并已将 psutil 包含为 amlcompute 中的 conda 依赖项。
    + 修复了预测数据集的启发式滞后和滚动窗口大小问题，其中的某些时序可能导致线性代数错误
      + 为预测运行中以试探法确定的参数添加了输出功能。
  + **azureml-contrib-datadrift**
    + 添加了当第一部分不存在数据集级别偏移时的保护，同时会创建输出指标。
  + **azureml-contrib-interpret**
    + azureml-contrib-explain-model 包已重命名为 azureml-contrib-interpret
  + **azureml-core**
    + 添加了用于取消注册数据集的 API。 `dataset.unregister_all_versions()`
    + azureml-contrib-explain-model 包已重命名为 azureml-contrib-interpret。
  + **[azureml-core](https://docs.microsoft.com/python/api/azureml-core)**
    + 添加了用于取消注册数据集的 API。 dataset.[unregister_all_versions()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_datastore.abstractdatastore#unregister--)。
    + 添加了用于检查数据更改时间的数据集 API。 `dataset.data_changed_time`.
    + 可以使用 `FileDataset` 和 `TabularDataset` 作为 Azure 机器学习管道中 `PythonScriptStep`、`EstimatorStep` 和 `HyperDriveStep` 的输入
    + 对于包含大量文件的文件夹，`FileDataset.mount` 的性能已得到改进
    + 可以使用 [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset) 和 [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset) 作为 Azure 机器学习管道中 [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep)、[EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep) 和 [HyperDriveStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyperdrivestep) 的输入
    + 对于包含大量文件的文件夹，FileDataset.[mount()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset#mount-mount-point-none----kwargs-) 的性能已得到改进
    + 在运行详细信息中添加了已知错误建议的 URL。
    + 修复了 run.get_metrics 中的一个 bug：如果运行包含过多的子级，请求将会失败
    + 修复了 [run.get_metrics](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run#get-metrics-name-none--recursive-false--run-type-none--populate-false-) 中的一个 bug：如果运行包含过多的子级，请求将会失败
    + 在 Arcadia 群集中添加了身份验证的支持。
    + 创建 Experiment 对象会在 Azure 机器学习工作区中获取或创建试验用于运行历史记录跟踪。 创建时会在 Experiment 对象中填充试验 ID 和存档时间。 示例：experiment = Experiment(workspace, "New Experiment") experiment_id = experiment.id archive() and reactivate() 是可以针对试验调用的函数，用于隐藏和还原试验，以避免在 UX 中显示该试验，或者在调用列出试验操作时按默认返回该试验。 如果使用与存档试验相同的名称创建了新的试验，可以在重新激活时通过传递新名称来重命名已存档的试验。 只能有一个具有给定名称的活动试验。 示例：experiment1 = Experiment(workspace, "Active Experiment") experiment1.archive() # 使用与存档试验相同的名称创建新的活动试验。 experiment2 = Experiment(workspace, "Active Experiment") experiment1.reactivate(new_name="Previous Active Experiment") 针对试验调用静态方法 list() 可以创建名称筛选器和 ViewType 筛选器。 ViewType 值为“ACTIVE_ONLY”、“ARCHIVED_ONLY”和“ALL”。示例：archived_experiments = Experiment.list(workspace, view_type="ARCHIVED_ONLY") all_first_experiments = Experiment.list(workspace, name="First Experiment", view_type="ALL")
    + 支持使用环境进行模型部署和服务更新
  + **azureml-datadrift**
    + DataDriftDector 类的 show 特性不再支持可选参数“with_details”。 show 特性只显示数据偏移系数和特征列的数据偏移分布。
    + DataDriftDetector 特性“get_output”的行为更改：
      + 输入参数 start_time 和 end_time 是可选的，而不是必需的；
      + 在同一调用中结合特定的 run_id 输入特定的 start_time 和/或 end_time 会导致值错误异常，因为它们是互斥的
      + 如果输入特定的 start_time 和/或 end_time，只会返回计划运行的结果；
      + 参数“daily_latest_only”已弃用。
    + 支持检索基于数据集的数据偏移输出。
  + **azureml-explain-model**
    + 将 AzureML-explain-model 包重命名为 AzureML-interpret，暂时保留旧包，以便能够后向兼容
    + 修复了 `automl` bug：从 ExplanationClient 下载时，原始解释默认设置为分类任务而不是回归
    + 添加了直接使用 `MimicWrapper` 创建 `ScoringExplainer` 的支持
  + **azureml-pipeline-core**
    + 改进了创建大管道的性能
  + **azureml-train-core**
    + 在 TensorFlow 评估器中添加了 TensorFlow 2.0 支持
  + **azureml-train-automl**
    + 创建 [Experiment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment) 对象会在 Azure 机器学习工作区中获取或创建试验用于运行历史记录跟踪。 创建时会在 Experiment 对象中填充试验 ID 和存档时间。 示例：

        ```py
        experiment = Experiment(workspace, "New Experiment")
        experiment_id = experiment.id
        ```
        [archive()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#archive--) 和 [reactivate()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#reactivate-new-name-none-) 是可以针对试验调用的函数，用于隐藏和还原试验，以避免在 UX 中显示该试验，或者在调用列出试验操作时按默认返回该试验。 如果使用与存档试验相同的名称创建了新的试验，可以在重新激活时通过传递新名称来重命名已存档的试验。 只能有一个具有给定名称的活动试验。 示例：

        ```py
        experiment1 = Experiment(workspace, "Active Experiment")
        experiment1.archive()
        # Create new active experiment with the same name as the archived.
        experiment2 = Experiment(workspace, "Active Experiment")
        experiment1.reactivate(new_name="Previous Active Experiment")
        ```
        针对试验调用静态方法 [list()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#list-workspace--experiment-name-none--view-type--activeonly---tags-none-) 可以创建名称筛选器和 ViewType 筛选器。 ViewType 值为“ACTIVE_ONLY”、“ARCHIVED_ONLY”和“ALL”。 示例：

        ```py
        archived_experiments = Experiment.list(workspace, view_type="ARCHIVED_ONLY")
        all_first_experiments = Experiment.list(workspace, name="First Experiment", view_type="ALL")
        ```
    + 支持使用环境进行模型部署和服务更新。
  + **[azureml-datadrift](https://docs.microsoft.com/python/api/azureml-datadrift)**
    + [DataDriftDetector](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector) 类的 show 特性不再支持可选参数“with_details”。 show 特性只显示数据偏移系数和特征列的数据偏移分布。
    + DataDriftDetector 函数 [get_output]https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector#get-output-start-time-none--end-time-none--run-id-none-) 的行为更改：
      + 输入参数 start_time 和 end_time 是可选的，而不是必需的；
      + 在同一调用中结合特定的 run_id 输入特定的 start_time 和/或 end_time 会导致值错误异常，因为它们是互斥的；
      + 如果输入特定的 start_time 和/或 end_time，只会返回计划运行的结果；
      + 参数“daily_latest_only”已弃用。
    + 支持检索基于数据集的数据偏移输出。
  + **azureml-explain-model**
    + 将 AzureML-explain-model 包重命名为 AzureML-interpret，暂时保留旧包，以便能够后向兼容。
    + 修复了 AutoML bug：从 ExplanationClient 下载时，原始解释默认设置为分类任务而不是回归
    + 添加了直接使用 MimicWrapper 创建 [ScoringExplainer](/python/api/azureml-interpret/azureml.interpret.scoring.scoring_explainer.scoringexplainer?view=azure-ml-py) 的支持
  + **[azureml-pipeline-core](https://docs.microsoft.com/python/api/azureml-pipeline-core)**
    + 改进了创建大管道的性能。
  + **[azureml-train-core](https://docs.microsoft.com/python/api/azureml-train-core)**
    + 在 [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow) 评估器中添加了 TensorFlow 2.0 支持。
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + 当设置迭代失败时，父运行将不再失败，因为业务流程可以处理此问题。
    + 为 AutoML 试验添加了 local-docker 和 local-conda 支持
    + 为 AutoML 试验添加了 local-docker 和 local-conda 支持。


## <a name="2019-10-08"></a>2019-10-08

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Azure 机器学习工作区的新 Web 体验（预览版）

[新工作区门户](https://ml.azure.com)中的“试验”选项卡已更新，使数据科学家能够以更高效的方式监视试验。 可以探索以下功能：
+ 查看试验元数据，轻松筛选和排序试验列表
+ 查看简化且高效的试验详细信息页，在其中可以可视化和比较运行
+ 探索显示运行详细信息页的新设计，以了解和监视训练运行

## <a name="2019-09-30"></a>2019-09-30

### <a name="azure-machine-learning-sdk-for-python-v1065"></a>适用于 Python 的 Azure 机器学习 SDK v1.0.65

  + **新功能**
    + 添加了组织有序的环境。 这些环境中已预先配置用于常用机器学习任务的库，并且已预先生成并缓存为 Docker 映像，以加快执行速度。 默认情况下，这些环境会显示在工作区的环境列表中，其前缀为“AzureML”。
    + 添加了组织有序的环境。 这些环境中已预先配置用于常用机器学习任务的库，并且已预先生成并缓存为 Docker 映像，以加快执行速度。 默认情况下，这些环境会显示在[工作区](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29)的环境列表中，其前缀为“AzureML”。

  + **azureml-train-automl**
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + 添加了对 ADB 和 HDI 的 ONNX 转换支持

+ **预览功能**
  + **azureml-train-automl**
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + 支持用作文本特征化器的 BERT 和 BiLSTM（仅限预览版）
    + 支持列用途和转换器参数的特征化自定义（仅限预览版）
    + 当用户在训练期间启用模型解释时支持原始解释（仅限预览版）
    + 添加了用作可训练管道的 Prophet 进行 `timeseries` 预测（仅限预览版）

  + **azureml-contrib-datadrift**
    + 已将包从 azureml-contrib-datadrift 重定位到 azureml-datadrift；未来的版本中将删除 `contrib` 包

+ **Bug 修复与改进**
  + **azureml-automl-core**
    + 已将 FeaturizationConfig 引入到 AutoMLConfig 和 AutoMLBaseSettings
    + 已将 FeaturizationConfig 引入到 [AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) 和 AutoMLBaseSettings
      + 使用给定的列和特征类型替代用于特征化的列用途
      + 替代转换器参数
    + 为 explain_model() 和 retrieve_model_explanations() 添加了弃用消息
    + 已添加 Prophet 作为可训练管道（仅限预览版）
    + 为 explain_model() 和 retrieve_model_explanations() 添加了弃用消息。
    + 已添加 Prophet 作为可训练管道（仅限预览版）。
    + 添加了自动检测目标滞后、滚动窗口大小和最大边际的支持。 如果某个 target_lags、target_rolling_window_size 或 max_horizon 设置为“auto”，将应用试探法来基于训练数据估算相应参数的值。
    + 修复了当数据集包含一个粒度列时的预测问题。此粒度为数字类型，训练集与测试集之间存在间隙
    + 修复了有关预测任务中的远程运行包含重复索引的错误消息
    + 修复了当数据集包含一个粒度列时的预测问题。此粒度为数字类型，训练集与测试集之间存在间隙。
    + 修复了有关预测任务中的远程运行包含重复索引的错误消息。
    + 添加了准则用于检查数据集是否不平衡。 如果是，则将准则消息写入控制台。
  + **azureml-core**
    + 添加了通过模型对象在存储中检索要建模的 SAS URL 的功能。 示例：model.get_sas_url()
    + 引入了 `run.get_details()['datasets']` 用于获取与已提交运行关联的数据集
    + 添加了 API `Dataset.Tabular.from_json_lines_files` 用于从 JSON 行文件创建 TabularDataset。 若要在 TabularDataset 上的 JSON 行文件中了解此表格数据， [请访问本文](how-to-create-register-datasets.md) 了解相关文档。
    + 为 supported_vmsizes() 函数添加了更多的 VM 大小字段（OS 磁盘、GPU 数目）
    + 为 list_nodes() 函数添加了更多的字段，以显示运行、专用 IP 和公共 IP、端口等。
    + 在群集预配期间可以指定新字段，可将 --remotelogin_port_public_access 设置为 enabled 或 disabled，具体取决于在创建群集时是要将 SSH 端口保留为打开还是关闭状态。 如果不指定此字段，服务将会根据是否在 VNet 中部署群集，智能地打开或关闭该端口。
  + **azureml-explain-model**
  + **[azureml-core](https://docs.microsoft.com/python/api/azureml-core/azureml.core)**
    + 添加了通过模型对象在存储中检索要建模的 SAS URL 的功能。 示例：model.[get_sas_url()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model#get-sas-urls--)
    + 引入了 run.[get_details](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29#get-details--)['datasets'] 用于获取与提交的运行关联的数据集
    + 添加了 API `Dataset.Tabular`.[from_json_lines_files()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-json-lines-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) 用于从 JSON 行文件创建 TabularDataset。 若要在有关 TabularDataset 的 JSON 行文件中了解此表格数据，请访问文档 https://aka.ms/azureml-data 。
    + 为 [supported_vmsizes()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#supported-vmsizes-workspace--location-none-) 函数添加了更多的 VM 大小字段（OS 磁盘、GPU 数目）
    + 为 [list_nodes()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#list-nodes--) 函数添加了更多的字段，以显示运行、专用 IP 和公共 IP、端口等。
    + 在群集[预配](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--)期间可以指定新字段，可将 `--remotelogin_port_public_access` 设置为 enabled 或 disabled，具体取决于在创建群集时是要将 SSH 端口保留为打开还是关闭状态。 如果不指定此字段，服务将会根据是否在 VNet 中部署群集，智能地打开或关闭该端口。
  + **azureml-explain-model**
    + 改进了分类方案中解释输出的文档。
    + 在评估示例的解释中添加了上传预测的 y 值的功能。 解锁了更有用的可视化效果。
    + 已将 explainer 属性添加到 MimicWrapper，以便能够获取基础 MimicExplainer。
  + **azureml-pipeline-core**
    + 添加了用于描述 Module、ModuleVersion 和 ModuleStep 的笔记本
  + **azureml-pipeline-steps**
    + 添加了用于通过 AML 管道支持 R 脚本运行的 RScriptStep。
    + 修复了 AzureBatchStep 中的元数据参数分析问题，此问题会导致出现错误消息“未指定参数 SubscriptionId 的分配”。
  + **azureml-train-automl**
    + 支持将 training_data、validation_data、label_column_name 和 weight_column_name 用作数据输入格式
    + 为 explain_model() 和 retrieve_model_explanations() 添加了弃用消息
  + **[azureml-pipeline-core](https://docs.microsoft.com/python/api/azureml-pipeline-core)**
    + 添加了一个[笔记本](https://aka.ms/pl-modulestep)来描述 [Module](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.module(class))、[ModuleVersion 和 [ModuleStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep)。
  + **[azureml-pipeline-steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps)**
    + 添加了用于通过 AML 管道支持 R 脚本运行的 [RScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.rscriptstep)。
    + 修复了 AzureBatchStep 中的元数据参数分析问题，此问题会导致出现错误消息“未指定参数 SubscriptionId 的分配”。
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + 支持将 training_data、validation_data、label_column_name 和 weight_column_name 用作数据输入格式。
    + 为 [explain_model()](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#explain-model-fitted-model--x-train--x-test--best-run-none--features-none--y-train-none----kwargs-) 和 [retrieve_model_explanations()](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#retrieve-model-explanation-child-run-) 添加了弃用消息。


## <a name="2019-09-16"></a>2019-09-16

### <a name="azure-machine-learning-sdk-for-python-v1062"></a>适用于 Python 的 Azure 机器学习 SDK v1.0.62

+ **新功能**
  + 针对 TabularDataset 引入了 `timeseries` 特征。 使用此特性可以轻松地按时间戳对 TabularDataset 的数据进行筛选，例如，获取某个时间范围内的所有数据或最新的数据。  https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb 示例笔记本。
  + 支持使用 TabularDataset 和 FileDataset 进行训练。 

  + **azureml-train-core**
      + 在 PyTorch 评估器中添加了 `Nccl` 和 `Gloo` 支持

+ **Bug 修复与改进**
  + **azureml-automl-core**
    + 已弃用 AutoML 设置“lag_length”和 LaggingTransformer。
    + 修复了输入数据（如果以数据流格式指定）的正确验证
    + 修改了 fit_pipeline.py 以生成图形 JSON 并上传到项目。
    + 使用 `Cytoscape` 在 `userrun` 下呈现图形。
  + **azureml-core**
    + 再次审视了 ADB 代码中的异常处理，并已根据新的错误处理对其进行更改
    + 为笔记本 VM 添加了自动 MSI 身份验证。
    + 修复了由于重试失败而可能上传损坏的模型或空模型的 bug。
    + 修复了当 `DataReference` 模式更改时 `DataReference` 名称发生更改的 bug（例如，调用 `as_upload`、`as_download` 或 `as_mount` 时）。
    + 使 `mount_point` 和 `target_path` 成为 `FileDataset.mount` 和 `FileDataset.download` 的可选参数。
    + 如果在未分配精细时间戳列或者删除分配的时间戳列的情况下调用时序相关的 API，将会引发找不到时间戳列的异常。
    + 应为时序列分配类型为 Date 的列，否则会发生异常
    + 分配 API“with_timestamp_columns”的时序列可以采用 None 值精细/粗糙时间戳列名称，这会清除以前分配的时间戳列。
    + 删除粗糙粒度或精细粒度时间戳列时，将引发异常，指示用户可以通过在删除列表中排除时间戳列来完成删除，或者结合 None 值调用 with_time_stamp 来释放时间戳列
    + 保留列列表中不包含粗糙粒度或精细粒度时间戳列时，将引发异常，指示用户可以通过在保留列列表中包含时间戳列来完成保留，或者结合 None 值调用 with_time_stamp 来释放时间戳列。
    + 添加了已注册模型大小的日志记录功能。
  + **azureml-explain-model**
    + 修复了当未安装“packaging”python 包时在控制台中输出的警告：“使用的 lightgbm 版本低于支持的版本，请升级到 2.2.1 或更高版本”
    + 修复了下载模型解释，提供包含许多特征的全局解释的分片
    + 在输出解释中修复了缺少初始化示例的模拟解释
    + 修复了使用两种不同类型的模型在解释客户端中上传期间 set 属性时发生的可变错误
    + 已将一个 get_raw 参数添加到评分 explainer.explain()，这样一个评分解释器就可以返回工程值和原始值。
  + **azureml-train-automl**
    + 引入了 AutoML 中的公共 API 以支持 `automl` 解释 SDK 中的解释 - 通过将 AutoML 特征化与解释 SDK 相耦合来支持 AutoML 解释的更新方式 - 为 AutoML 模型集成了 azureml 解释 SDK 中的原始解释支持。
    + 从远程训练环境中删除了 azureml-defaults。
    + 对于 Azure Databricks 代码路径中的 AutoML，已将默认缓存存储位置从基于 FileCacheStore 的位置更改为 AzureFileCacheStore 位置。
    + 修复了输入数据（如果以数据流格式指定）的正确验证
  + **azureml-train-core**
    + 已撤消 source_directory_data_store 弃用。
    + 添加了替代 azureml 安装的包版本的功能。
    + 在评估器的 `environment_definition` 参数中添加了 dockerfile 支持。
    + 在评估器中简化了分布式训练参数。

         ```py
        from azureml.train.dnn import TensorFlow, Mpi, ParameterServer
        ```

## <a name="2019-09-09"></a>2019-09-09

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Azure 机器学习工作区的新 Web 体验（预览版）
数据科学家和数据工程师可以使用新的 Web 体验，在单个位置完成从准备和可视化数据，到训练和部署模型的端到端机器学习生命周期。

![Azure 机器学习工作区 UI（预览版）](./media/azure-machine-learning-release-notes/new-ui-for-workspaces.jpg)

**主要功能：**

现在，使用此新的 Azure 机器学习界面可以：
+ 管理笔记本或链接到 Jupyter
+ [运行自动化机器学习试验](tutorial-first-experiment-automated-ml.md)
+ [从本地文件、数据存储和 Web 文件创建数据集](how-to-create-register-datasets.md)
+ 探索和准备用于创建模型的数据集
+ 监视模型的数据偏移
+ 从仪表板查看最近使用的资源

目前，此版本支持以下浏览器：Chrome、Firefox、Safari 和 Microsoft Edge 预览版。

**已知问题：**

1. 如果当部署正在进行时出现“出现问题! 加载区块文件时出错”，请刷新浏览器。

1. 无法删除或重命名笔记本和文件中的文件。 在公共预览版中，可以在笔记本 VM 中使用 Jupyter UI 或终端执行更新文件操作。 由于它是一个装载的网络文件系统，在笔记本 VM 上做出的所有更改会立即反映在笔记本工作区中。

1. 通过 SSH 连接到笔记本 VM：
   1. 找到在设置 VM 期间创建的 SSH 密钥。 或者，在 Azure 机器学习工作区中找到密钥 > 打开“计算”选项卡 > 在列表中找到笔记本 VM > 打开其属性：复制对话框中的密钥。
   1. 将这些公共和专用 SSH 密钥导入本地计算机。
   1. 使用这些密钥通过 SSH 连接到笔记本 VM。

## <a name="2019-09-03"></a>2019-09-03
### <a name="azure-machine-learning-sdk-for-python-v1060"></a>适用于 Python 的 Azure 机器学习 SDK v1.0.60

+ **新功能**
  + 引入了 FileDataset，它引用数据存储或公共 URL 中的单个或多个文件。 这些文件可以采用任何格式。 FileDataset 提供下载文件或将其装载到计算资源的功能。 
  + 为 PythonScript 步骤、Adla 步骤、Databricks 步骤、DataTransferStep 和 AzureBatch 步骤添加了管道 Yaml 支持

+ **Bug 修复与改进**
  + **azureml-automl-core**
    + AutoArima 目前是仅适用于预览版的可建议管道。
    + 改进了预测的错误报告。
    + 使用自定义异常而不是预测任务中的泛型改进了日志记录。
    + 删除了检查 max_concurrent_iterations 是否小于迭代总数的行为。
    + AutoML 模型现在返回 AutoMLExceptions
    + 此版本改进了自动化机器学习本地运行的执行性能。
  + **azureml-core**
    + 引入了 Dataset.get_all(workspace)，它会返回按注册名称进行键控的 `TabularDataset` 和 `FileDataset` 对象的字典。

    ```py
    workspace = Workspace.from_config()
    all_datasets = Dataset.get_all(workspace)
    mydata = all_datasets['my-data']
    ```

    + 引入了 `parition_format` 作为 `Dataset.Tabular.from_delimited_files` 和 `Dataset.Tabular.from_parquet.files` 的参数。 将会基于指定的格式将每个数据路径的分区信息提取到列中。 “{column_name}”创建字符串列，“{column_name:yyyy/MM/dd/HH/mm/ss}”创建日期时间列，其中，“yyyy”、“MM”、“dd”、“HH”、“mm”和“ss”用于提取日期时间类型的年、月、日、小时、分钟和秒。 partition_format 应从文件路径结束之前的第一个分区键位置开始。 例如，假设路径“../USA/2019/01/01/data.csv”中的分区按国家/地区和时间提供，则 partition_format='/{Country}/{PartitionDate:yyyy/MM/dd}/data.csv' 将创建值为“USA”的字符串列“Country”，以及值为“2019-01-01”的日期时间列“PartitionDate”。
        ```py
        workspace = Workspace.from_config()
        all_datasets = Dataset.get_all(workspace)
        mydata = all_datasets['my-data']
        ```

    + 引入了 `partition_format` 作为 `Dataset.Tabular.from_delimited_files` 和 `Dataset.Tabular.from_parquet.files` 的参数。 将会基于指定的格式将每个数据路径的分区信息提取到列中。 “{column_name}”创建字符串列，“{column_name:yyyy/MM/dd/HH/mm/ss}”创建日期时间列，其中，“yyyy”、“MM”、“dd”、“HH”、“mm”和“ss”用于提取日期时间类型的年、月、日、小时、分钟和秒。 partition_format 应从文件路径结束之前的第一个分区键位置开始。 例如，假设路径“../USA/2019/01/01/data.csv”中的分区按国家/地区和时间提供，则 partition_format='/{Country}/{PartitionDate:yyyy/MM/dd}/data.csv' 将创建值为“USA”的字符串列“Country”，以及值为“2019-01-01”的日期时间列“PartitionDate”。
    + 已将 `to_csv_files` 和 `to_parquet_files` 方法添加到 `TabularDataset`。 这些方法通过将数据转换为指定格式的文件来实现 `TabularDataset` 与 `FileDataset` 之间的转换。
    + 保存 Model.package() 生成的 Dockerfile 时，将自动记录到基础映像注册表中。
    + 不再需要“gpu_support”；AML 现在会自动检测并使用 nvidia docker 扩展（如果可用）。 未来的版本中将删除“gpu_support”。
    + 添加了创建、更新和使用 PipelineDrafts 的支持。
    + 此版本改进了自动化机器学习本地运行的执行性能。
    + 用户可以按名称从运行历史记录中查询指标。
    + 使用自定义异常而不是预测任务中的泛型改进了日志记录。
  + **azureml-explain-model**
    + 已将 feature_maps 参数添加到新的 MimicWrapper，使用户能够获取原始特征解释。
    + 现在默认会关闭数据集上传已进行解释上传，可以使用 upload_datasets=True 重新启用它。
    + 已将“is_law”筛选参数添加到解释列出和下载函数。
    + 已将方法 `get_raw_explanation(feature_maps)` 添加到全局和本地解释对象。
    + 已将版本检查添加到 lightgbm，低于支持的版本时会输出警告
    + 优化了批处理解释时的内存用量
    + AutoML 模型现在返回 AutoMLExceptions
  + **azureml-pipeline-core**
    + 添加了创建、更新和使用 PipelineDrafts 的支持 - 可用于维护可变管道定义，并以交互方式使用它们来运行
  + **azureml-train-automl**
    + 创建了用于安装特定版本的支持 GPU 的 pytorch v1.1.0、:::no-loc text="cuda"::: 工具包 9.0、pytorch-transformers 的功能，在远程 python 运行时环境中启用 BERT/XLNet 必须使用此功能。
  + **azureml-train-core**
    + 某些超参数空间定义错误直接在 SDK 中（而不是服务器端）导致提前失败。

### <a name="azure-machine-learning-data-prep-sdk-v1114"></a>Azure 机器学习数据准备 SDK v1.1.14
+ **Bug 修复与改进**
  + 已实现使用原始路径和凭据写入 ADLS/ADLSGen2 的功能。
  + 修复了导致 `include_path=True` 对 `read_parquet` 不起作用的 bug。
  + 修复了异常“无效的属性值: hostSecret”导致的 `to_pandas_dataframe()` 失败。
  + 修复了在 Spark 模式下无法在 DBFS 中读取文件的 bug。

## <a name="2019-08-19"></a>2019-08-19

### <a name="azure-machine-learning-sdk-for-python-v1057"></a>适用于 Python 的 Azure 机器学习 SDK v1.0.57
+ **新功能**
  + 使 `TabularDataset` 能够被 AutomatedML 使用。 有关 `TabularDataset` 的详细信息，请访问 https://aka.ms/azureml/howto/createdatasets 。

+ **Bug 修复与改进**
  + **azure-cli-ml**
    + 现在，可为 AKS 群集上部署的评分终结点更新 Microsoft 生成的证书和客户证书的 TLS/SSL 证书。
  + **azureml-automl-core**
    + 修复了 AutoML 中的一个问题：不正确删除缺少标签的行。
    + 改进了 AutoML 中的错误日志记录；现在，完整的错误消息始终会写入日志文件。
    + AutoML 已更新其包固定方式，使其包含 `azureml-defaults`、`azureml-explain-model` 和 `azureml-dataprep`。 存在包不匹配的情况时（`azureml-train-automl` 包除外），AutoML 将不再发出警告。
    + 修复了 `timeseries` 中的一个问题：cv 拆分的大小不相等，导致 bin 计算失败。
    + 针对交叉验证训练类型运行系综迭代时，如果我们在下载针对整个数据集训练的模型时遇到问题，则模型权重与馈送到投票系综的模型之间会出现不一致情况。
    + 修复了以 pandas 数据帧格式而不是以 numpy 数组形式提供训练和/或验证标签（y 和 y_valid）时引发的错误。
    + 修复了在输入表的布尔值列中遇到 None 时预测任务出现的问题。
    + 允许 AutoML 用户在预测时删除不够长的训练系列。 - 允许 AutoML 用户在预测时从测试集中删除不在训练集中的粒度。
  + **azureml-core**
    + 修复了 blob_cache_timeout 参数排序的问题。
    + 已将外部拟合与转换异常类型添加到系统错误。
    + 添加了对远程运行使用 Key Vault 机密的支持。 添加了 azureml.core.keyvault.Keyvault 类，用于在 keyvault 中添加、获取和列出与工作区关联的机密。 支持的操作为：
      + azureml.core.workspace.Workspace.get_default_keyvault()
      + azureml.core.keyvault.Keyvault.set_secret(name, value)
      + azureml.core.keyvault.Keyvault.set_secrets(secrets_dict)
      + azureml.core.keyvault.Keyvault.get_secret(name)
      + azureml.core.keyvault.Keyvault.get_secrets(secrets_list)
      + azureml.core.keyvault.Keyvault.list_secrets()
    + 用于在远程运行期间获取默认 keyvault 和获取机密的其他方法：
      + azureml.core.workspace.Workspace.get_default_keyvault()
      + azureml.core.run.Run.get_secret(name)
      + azureml.core.run.Run.get_secrets(secrets_list)
    + 已将更多的重写参数添加到 submit-hyperdrive CLI 命令。
    + 改进了 API 调用将重试扩展到常见请求库异常的可靠性。
    + 添加了从已提交运行提交运行的支持。
    + 修复了 FileWatcher 中的 SAS 令牌过期问题，此问题会导致文件在其初始令牌过期后停止上传。
    + 支持在数据集 python SDK 中导入 HTTP csv/tsv 文件。
    + 已弃用 Workspace.setup() 方法。 显示给用户的警告消息建议改用 create() 或 get()/from_config()。
    + 添加了 Environment.add_private_pip_wheel()，以便能够将专用自定义 python 包 `whl` 上传到工作区，并安全地使用这些包生成/具体化环境。
    + 现在，可为 AKS 群集上部署的评分终结点更新 Microsoft 生成的证书和客户证书的 TLS/SSL 证书。
  + **azureml-explain-model**
    + 添加了相应的参数，用于在上传时将模型 ID 添加到解释中。
    + 已将 `is_raw` 标记添加到内存和上传内容中的解释。
    + 已针对 azureml-explain-model 包添加了 pytorch 支持和测试。
  + **azureml-opendatasets**
    + 支持检测和记录自动测试环境。
    + 添加了用于按县和邮政编码获取美国人口数据的类。
  + **azureml-pipeline-core**
    + 添加了用于输入和输出端口定义的标签属性。
  + **azureml-telemetry**
    + 修复了错误的遥测配置。
  + **azureml-train-automl**
    + 修复了以下 bug：设置失败时，错误不会记录到设置运行的“errors”字段中，因而不会存储在父运行的“errors”中。
    + 修复了 AutoML 中的一个问题：不正确删除缺少标签的行。
    + 允许 AutoML 用户在预测时删除不够长的训练系列。
    + 允许 AutoML 用户在预测时从测试集中删除不在训练集中的粒度。
    + 现在，AutoMLStep 会将 `automl` 配置传递到后端，以避免在更改或添加新的配置参数时发生任何问题。
    + AutoML Data Guardrail 现已推出公共预览版。 在训练后，用户将看到 Data Guardrail 报告（适用于分类/回归任务），并且可以通过 SDK API 访问该报告。
  + **azureml-train-core**
    + 在 PyTorch 评估器中添加了 torch 1.2 支持。
  + **azureml-widgets**
    + 改进了分类训练的混淆矩阵图。

### <a name="azure-machine-learning-data-prep-sdk-v1112"></a>Azure 机器学习数据准备 SDK v1.1.12
+ **新功能**
  + 现在可将字符串列表作为输入传递到 `read_*` 方法。

+ **Bug 修复与改进**
  + 在 Spark 中运行时，`read_parquet` 的性能已得到显著提升。
  + 修复了以下问题：当单个列的日期格式不明确时，`column_type_builder` 会失败。

### <a name="azure-portal"></a>Azure 门户
+ **预览版功能**
  + 现在为运行详细信息页提供日志和输出文件流。 当预览切换开关处于打开状态时，这些文件将实时流式处理更新。
  + 预览版中发布了在工作区级别设置配额的功能。 AmlCompute 配额将在订阅级别分配，但我们现在允许在工作区之间分发该配额，并分配该配额来实现公平共享和监管。 只需单击工作区左侧导航栏中的“用量 + 配额”边栏选项卡，然后选择“配置配额”选项卡即可。 请注意，只有订阅管理员才能在工作区级别设置配额，因为这是一个跨工作区的操作。

## <a name="2019-08-05"></a>2019-08-05

### <a name="azure-machine-learning-sdk-for-python-v1055"></a>适用于 Python 的 Azure 机器学习 SDK v1.0.55

+ **新功能**
  + 现在，针对 AKS 上部署的评分终结点发出的调用支持基于令牌的身份验证。 我们将继续支持当前的基于密钥的身份验证，用户每次可以使用其中的一种身份验证机制。
  + 可将虚拟网络 (VNet) 后面的 Blob 存储注册为数据存储。

+ **Bug 修复与改进**
  + **azureml-automl-core**
    + 修复了以下 bug：CV 拆分的验证大小较小，导致为回归和预测生成错误的预测图表，而不是真实的图表。
    + 远程运行上的预测任务的日志记录已得到改进，现在，如果运行失败，将为用户提供详细的错误消息。
    + 修复了预处理标志为 True 时 `Timeseries` 失败的问题。
    + 使某些预测数据验证错误消息更具可操作性。
    + 通过删除和/或推迟加载数据集（尤其是在进程生成之间），减少了 AutoML 运行的内存消耗
  + **azureml-contrib-explain-model**
    + 已将 model_task 标志添加到解释器，使用户能够替代模型类型的默认自动推理逻辑
    + 小组件更改：会连同 `contrib` 一起自动安装，不再需要指定 `nbextension` install/enable - 支持仅包含全局特征重要性（例如 Permutative）的解释
    + 仪表板更改：- 摘要页上提供框图、小提琴图和 `beeswarm` 图 -“Top-k”滑块更改时可以更快地重新呈现 `beeswarm` 图 - 有帮助的消息会解释 top-k 的计算方式 - 未提供数据时，有用的可自定义消息会取代图表
  + **azureml-core**
    + 添加了 Model.package() 方法，以创建 Docker 映像，以及用于封装模型及其依赖项的 Dockerfile。
    + 更新了本地 Web 服务，以接受包含 Environment 对象的 InferenceConfigs。
    + 修复了在将“.”（表示当前目录）作为 model_path 参数传递时，Model.register() 生成无效模型的问题。
    + 添加了 Run.submit_child，在将运行指定为已提交子运行的父级时，该功能将镜像 Experiment.submit。
    + 支持 Run.register_model 中 Model.register 的配置选项。
    + 可在现有群集上运行 JAR 作业。
    + 现在支持 instance_pool_id 和 cluster_log_dbfs_path 参数。
    + 添加了在将模型部署到 Web 服务时使用 Environment 对象的支持。 现在，可将 Environment 对象作为 InferenceConfig 对象的一部分提供。
    + 为新区域（centralus、westus、northcentralus）添加了 appinsifht 映射
    + 为所有 Datastore 类中的所有特性添加了文档。
    + 已将 blob_cache_timeout 参数添加到 `Datastore.register_azure_blob_container`。
    + 已将 save_to_directory 和 load_from_directory 方法添加到 azureml.core.environment.Environment。
    + 已将“az ml environment download”和“az ml environment register”命令添加到 CLI。
    + 添加了 Environment.add_private_pip_wheel 方法。
  + **azureml-explain-model**
    + 已使用数据集服务（预览版）将数据集跟踪添加到解释。
    + 已将流式处理全局解释时的默认批大小从 10k 减至 100。
    + 已将 model_task 标志添加到解释器，使用户能够替代模型类型的默认自动推理逻辑。
  + **azureml-mlflow**
    + 修复了 mlflow.azureml.build_image 中忽略嵌套目录的 bug。
  + **azureml-pipeline-steps**
    + 添加了在现有 Azure Databricks 群集上运行 JAR 作业的功能。
    + 添加了对 DatabricksStep 步骤使用 instance_pool_id 和 cluster_log_dbfs_path 参数的支持。
    + 添加了在 DatabricksStep 步骤中使用管道参数的支持。
  + **azureml-train-automl**
    + 为系综相关文件添加了 `docstrings`。
    + 更新了 `max_cores_per_iteration` 和 `max_concurrent_iterations` 的文档，以介绍更多的相应语言
    + 远程运行上的预测任务的日志记录已得到改进，现在，如果运行失败，将为用户提供详细的错误消息。
    + 从管道 `automlstep` 笔记本中删除了 get_data。
    + 在 `automlstep` 中开始支持 `dataprep`。

### <a name="azure-machine-learning-data-prep-sdk-v1110"></a>Azure 机器学习数据准备 SDK v1.1.10

+ **新功能**
  + 现在，可以请求对特定的列执行特定的检查器（例如直方图、散点图等）。
  + 已将 parallelize 参数添加到 `append_columns`。 如果此参数为 True，则将数据载入内存，但执行将并行运行；如果为 False，则执行将流式处理，但使用单线程。

## <a name="2019-07-23"></a>2019-07-23

### <a name="azure-machine-learning-sdk-for-python-v1053"></a>适用于 Python 的 Azure 机器学习 SDK v1.0.53

+ **新功能**
  + 自动化机器学习现在支持在远程计算目标上训练 ONNX 模型
  + Azure 机器学习现在提供从上一个运行、检查点或模型文件恢复训练的功能。
    + 了解如何[使用评估器从前一个运行恢复训练](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/training/train-tensorflow-resume-training/train-tensorflow-resume-training.ipynb)

+ **Bug 修复与改进**
  + **azure-cli-ml**
    + CLI 命令“model deploy”和“service update”现在接受参数、配置文件或二者的组合。 文件中的参数优先于特性。
    + 现在，在注册后可以更新模型说明
  + **azureml-automl-core**
    + 将 NimbusML 依赖项更新为版本 1.2.0（当前的最新版本）。
    + 即将添加在 AutoML 评估器中使用 NimbusML 评估器和管道的支持。
    + 即将修复系综选择过程中的一个 bug：即使评分保持恒定，此 bug 也会不必要地增大生成的系综。
    + 允许跨预测任务的 CV 拆分重复使用某些特征化。 对于高开销的特征化（例如滞后和滚动窗口），这可以大致按 n_cross_validations 的系数加快设置运行的运行时。
    + 即将解决当时间超出 pandas 支持的时间范围时发生的某个问题。 现在，如果时间小于 pd.Timestamp.min 或大于 pd.Timestamp.max，将引发 DataException
    + 如果训练集和测试集可以对齐，则预测现在允许在这些集中使用不同的频率。 例如，“从一月开始按季度”和“从十月开始按季度”可以对齐。
    + 已将“parameters”属性添加到 TimeSeriesTransformer。
    + 删除了旧异常类。
    + 在预测任务中，`target_lags` 参数现在接受单个整数值，或整数列表。 如果提供整数，则只会创建一个滞后对象。 如果提供列表，则采用滞后对象的唯一值。 target_lags=[1, 2, 2, 4] 将创建 1 个、2 个和 4 个时段的滞后对象。
    + 修复了在转换后丢失列类型的 bug（链接的 bug）；
    + 在 `model.forecast(X, y_query)` 中，允许 y_query 是开头包含 None 的对象类型 (#459519)。
    + 已将预期值添加到 `automl` 输出
  + **azureml-contrib-datadrift**
    +  已改进示例笔记本，包括切换到 azureml-opendatasets 而不是 azureml-contrib-opendatasets；扩充数据时的性能已得到改进
  + **azureml-contrib-explain-model**
    + 在 azureml-contrib-explain-model 包中修复了 LIME 解释器的原始特征重要性的 transformations 参数
    + 已在 AzureML-contrib-explain-model 包中将分段添加到映像解释器中的映像解释
    + 为 LimeExplainer 添加了 scipy 稀疏支持
    + 添加了 `batch_size` 用于在 `include_local=False` 时模拟解释器，以便分批流式处理全局解释来改善 DecisionTreeExplainableModel 的执行时间
  + **azureml-contrib-featureengineering**
    + 修复了 set_featurizer_timeseries_params() 的调用：dict 值类型更改和 null 检查 - 为 `timeseries` 特征化器添加了笔记本
    + 将 NimbusML 依赖项更新为版本 1.2.0（当前的最新版本）。
  + **azureml-core**
    + 添加了在 AzureML CLI 中附加 DBFS 数据存储的功能
    + 修复了数据存储上传时存在的以下 bug：如果 `target_path` 以 `/` 开头，会创建一个空文件夹
    + 修复了 ServicePrincipalAuthentication 中的 `deepcopy` 问题。
    + 已将“az ml environment show”和“az ml environment list”命令添加到 CLI。
    + 现在，环境支持指定 base_dockerfile 作为已生成的 base_image 的替代项。
    + 未使用的 RunConfiguration 设置 auto_prepare_environment 已标记为已弃用。
    + 现在，在注册后可以更新模型说明
    + Bug 修复：现在，如果由于上游依赖项的原因导致删除失败，模型和映像删除操作现在会提供有关检索上游对象的更多信息。
    + 修复了以下 bug：在为某些环境创建工作区时输出部署的空白持续时间。
    + 改进了工作区创建失败异常。 用户不再会看到“无法创建工作区。 找不到...”消息，而是看到实际的创建失败。
    + 在 AKS Web 服务中添加了令牌身份验证的支持。
    + 已将 `get_token()` 方法添加到 `Webservice` 对象。
    + 添加了管理机器学习数据集的 CLI 支持。
    + `Datastore.register_azure_blob_container` 现在可以选择性地采用 `blob_cache_timeout` 值（以秒为单位），该值配置 blobfuse 的 mount 参数，以便为此数据存储启用缓存过期。 默认值为无超时，即，在读取某个 Blob 时，该 Blob 将保留在本地缓存中，直到作业完成。 大多数作业优先使用此设置，但某些作业需要从大型数据集读取更多数据，而其节点无法容纳此数据集。 优化此参数有助于这些作业成功完成。 优化此参数时请小心：设置太小的值可能会导致性能不佳，因为在 epoch 中使用的数据在再次使用之前可能会过期。 这意味着，将从 Blob 存储（即网络）而不是本地缓存完成所有读取，这会对训练时间产生负面影响。
    + 现在，在注册后可以正常更新模型说明
    + 模型和映像删除操作现在会提供有关依赖于它们的上游对象（这些对象导致删除失败）的更多信息
    + 使用 azureml.mlflow 改进了远程运行的资源利用率。
  + **azureml-explain-model**
    + 在 azureml-contrib-explain-model 包中修复了 LIME 解释器的原始特征重要性的 transformations 参数
    + 为 LimeExplainer 添加了 scipy 稀疏支持
    + 添加了造型线性解释器包装器，以及用于解释线性模型的另一级表格解释器
    + 对于解释模型库中的模拟解释器，修复了 include_local=False 时稀疏数据输入的错误
    + 已将预期值添加到 `automl` 输出
    + 修复了在提供 transformations 参数获取原始特征重要性时的排列特征重要性
    + 添加了 `batch_size` 用于在 `include_local=False` 时模拟解释器，以便分批流式处理全局解释来改善 DecisionTreeExplainableModel 的执行时间
    + 对于模型可解释性库，修复了需要为预测提供 pandas 数据帧输入时的黑盒解释器
    + 修复了以下 bug：`explanation.expected_values` 有时返回浮点数，而不是返回包含浮点数的列表。
  + **azureml-mlflow**
    + 改进了 mlflow.set_experiment(experiment_name) 的性能
    + 修复了对 mlflow tracking_uri 使用 InteractiveLoginAuthentication 时出现的 bug
    + 使用 azureml.mlflow 改进了远程运行的资源利用率。
    + 改进了 azureml-mlflow 包的文档
    + 修补了以下 bug：mlflow.log_artifacts("my_dir") 将项目保存在“my_dir/<artifact-paths>”而不是“<artifact-paths>”下
  + **azureml-opendatasets**
    + 由于最近出现的内存问题，已将 `opendatasets` 的 `pyarrow` 关联到旧版本 (<0.14.0)。
    + 已将 azureml-contrib-opendatasets 移到 azureml-opendatasets。
    + 允许将打开的数据集类注册到 Azure 机器学习工作区并无缝利用 AML 数据集功能。
    + 显著改进了非 SPARK 版本中的 NoaaIsdWeather 扩充性能。
  + **azureml-pipeline-steps**
    + DatabricksStep 中的输入和输出现在支持 DBFS 数据存储。
    + 更新了 Azure Batch 步骤的输入/输出相关文档。
    + 在 AzureBatchStep 中，已将 *delete_batch_job_after_finish* 默认值更改为 *true*。
  + **azureml-telemetry**
    +  已将 azureml-contrib-opendatasets 移到 azureml-opendatasets。
    + 允许将打开的数据集类注册到 Azure 机器学习工作区并无缝利用 AML 数据集功能。
    + 显著改进了非 SPARK 版本中的 NoaaIsdWeather 扩充性能。
  + **azureml-train-automl**
    + 更新了有关 get_output 的文档以反映实际返回类型，并提供有关检索关键属性的附加说明。
    + 将 NimbusML 依赖项更新为版本 1.2.0（当前的最新版本）。
    + 已将预期值添加到 `automl` 输出
  + **azureml-train-core**
    + 现在支持将字符串用作自动超参数优化的计算目标
    + 未使用的 RunConfiguration 设置 auto_prepare_environment 已标记为已弃用。

### <a name="azure-machine-learning-data-prep-sdk-v119"></a>Azure 机器学习数据准备 SDK v1.1.9

+ **新功能**
  + 添加了对从 HTTP 或 HTTPS URL 直接读取文件的支持。

+ **Bug 修复与改进**
  + 改进了尝试从远程源（当前不受支持）读取 Parquet 数据集时显示的错误消息。
  + 修复了在 ADLS Gen 2 中写入 Parquet 文件格式，以及在路径中更新 ADLS Gen 2 容器名称时出现的 bug。

## <a name="2019-07-09"></a>2019-07-09

### <a name="visual-interface"></a>可视化界面
+ **预览功能**
  + 在可视化界面中添加了“执行 R 脚本”模块。

### <a name="azure-machine-learning-sdk-for-python-v1048"></a>适用于 Python 的 Azure 机器学习 SDK v1.0.48

+ **新功能**
  + **azureml-opendatasets**
    + **azureml-contrib-opendatasets** 现已作为 **azureml-opendatasets** 提供。 旧包仍可正常运行，但我们建议使用 **azureml-opendatasets**，以获得更丰富的功能和改进。
    + 使用此新包可将打开的数据集注册为 Azure 机器学习工作区中的数据集，并利用数据集提供的任何功能。
    + 它还包括一些现有的功能，例如，将打开的数据集用作 Pandas/SPARK 数据帧，以及某些数据集（如天气）的位置联接。

+ **预览功能**
    + HyperDriveConfig 现在接受将管道对象用作参数，以支持使用管道进行超参数优化。

+ **Bug 修复与改进**
  + **azureml-train-automl**
    + 修复了在转换后丢失列类型的 bug。
    + 修复了 bug，允许 y_query 是开头包含 None 的对象类型。
    + 修复了系综选择过程中的以下问题：即使评分保持恒定，此 bug 也会不必要地增大生成的系综。
    + 解决了在 AutoMLStep 中允许 list_models 和块 list_models 设置的问题。
    + 修复了在 Azure ML 管道上下文中使用 AutoML 时阻止使用预处理的问题。
  + **azureml-opendatasets**
    + 已将 azureml-contrib-opendatasets 移到 azureml-opendatasets。
    + 允许将打开的数据集类注册到 Azure 机器学习工作区并无缝利用 AML 数据集功能。
    + 显著改进了非 SPARK 版本中的 NoaaIsdWeather 扩充性能。
  + **azureml-explain-model**
    + 更新了 interpretability 对象的联机文档。
    + 对于模型可解释性库，添加了 `batch_size` 用于在 `include_local=False` 时模拟解释器，以便分批流式处理全局解释来改善 DecisionTreeExplainableModel 的执行时间
    + 修复了以下问题：`explanation.expected_values` 有时返回浮点数，而不是返回包含浮点数的列表。
    + 已将预期值添加到解释模型库中模拟解释器的 `automl` 输出。
    + 修复了在提供 transformations 参数获取原始特征重要性时的排列特征重要性。
  + **azureml-core**
    + 添加了在 AzureML CLI 中附加 DBFS 数据存储的功能。
    + 修复了数据存储上传时存在的以下问题：如果 `target_path` 以 `/` 开头，会创建一个空文件夹。
    + 启用了两个数据集的比较。
    + 现在，如果由于上游依赖项的原因导致删除失败，模型和映像删除操作现在会提供有关检索上游对象的更多信息。
    + 已弃用 auto_prepare_environment 中未使用的 RunConfiguration 设置。
  + **azureml-mlflow**
    + 改进了使用 azureml.mlflow 的远程运行的资源利用率。
    + 改进了 azureml-mlflow 包的文档。
    + 修复了以下问题：mlflow.log_artifacts("my_dir") 将项目保存在“my_dir/artifact-paths”而不是“artifact-paths”下。
  + **azureml-pipeline-core**
    + 所有管道步骤的参数 hash_paths 已弃用，未来将会删除。 默认情况下，source_directory 的内容会经过哈希处理（.amlignore 或 .gitignore 中列出的文件除外）
    + 正在持续改进 Module 和 ModuleStep，以支持计算类型特定的模块，为 RunConfiguration 集成做好准备；将做出其他更改以解锁管道中计算类型特定的模块的使用。
  + **azureml-pipeline-steps**
    + AzureBatchStep：改进了有关输入/输出的文档。
    + AzureBatchStep：已将 delete_batch_job_after_finish 默认值更改为 true。
  + **azureml-train-core**
    + 现在支持将字符串用作自动超参数优化的计算目标。
    + 已弃用 auto_prepare_environment 中未使用的 RunConfiguration 设置。
    + 已弃用参数 `conda_dependencies_file_path` 和 `pip_requirements_file_path`，分别以 `conda_dependencies_file` 和 `pip_requirements_file` 取代。
  + **azureml-opendatasets**
    + 显著改进了非 SPARK 版本中的 NoaaIsdWeather 扩充性能。

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033-released"></a>已发布适用于 Python 的 Azure 机器学习 SDK v1.0.33。

+ [FPGA](how-to-deploy-fpga-web-service.md) 上的 Azure ML 硬件加速模型已推出正式版。
  + 现在可以[使用 azureml-accel-models 包](how-to-deploy-fpga-web-service.md)来：
    + 训练支持的深度神经网络（ResNet 50、ResNet 152、DenseNet-121、VGG-16 和 SSD-VGG）的权重
    + 结合支持的 DNN 使用迁移学习
    + 将模型注册到模型管理服务并将模型容器化
    + 在 Azure Kubernetes 服务 (AKS) 群集中使用 FPGA 将模型部署到 Azure VM
  + 将容器部署到 [Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview) 服务器设备
  + 参考此[示例](https://github.com/Azure-Samples/aml-hardware-accelerated-models)使用 gRPC 终结点为数据评分

### <a name="automated-machine-learning"></a>自动化机器学习

+ 使用功能扫描可以动态添加 :::no-loc text="featurizers"::: 进行性能优化。 新的 :::no-loc text="featurizers":::：工作嵌入项、证据权重、目标编码、文本目标编码、群集距离
+ 智能 CV 可以处理自动化 ML 中的训练/有效拆分
+ 少量的内存优化更改和运行时性能改进
+ 模型解释的性能改进
+ 本地运行的 ONNX 模型转换
+ 添加了子采样支持
+ 未定义退出条件时智能停止
+ 堆栈式系综

+ 时序预测
  + 新的预测函数
  + 现在可对时序数据使用滚动源交叉验证
  + 已添加新功能来配置时序滞后
  + 已添加新功能来支持滚动窗口聚合功能
  + 在试验设置中定义国家/地区代码时可以使用新的 Holiday 检测和特征化器

+ Azure Databricks
  + 已实现时序预测和模型可解释性/可转译性功能
  + 现在可以取消和恢复（继续）自动化 ML 试验
  + 添加了对多核处理的支持

### <a name="mlops"></a>MLOps
+ **用于评分容器的本地部署和调试**<br/> 现在，可在本地部署 ML 模型并快速迭代评分文件和依赖项，以确保其行为符合预期。

+ **引入了 InferenceConfig & Model.deploy()**<br/> 模型部署现在支持使用入口脚本指定源文件夹，这与 RunConfig 的行为相同。  此外，模型部署已简化成单个命令。

+ **Git 参考跟踪**<br/> 一段时间以来，客户一直在请求基本的 Git 集成功能，因为它有助于维护端到端的审核线索。 我们已跨 Azure ML 中的主要实体为跨 Git 相关的元数据（存储库、提交、清理状态）实现了跟踪。 此信息将由 SDK 和 CLI 自动收集。

+ **模型分析和验证服务**<br/> 客户经常抱怨难以正确调整与其推理服务关联的计算大小。 使用我们的模型分析服务，客户可以提供示例输入，而我们将会分析 16 个不同的 CPU/内存配置，以确定部署规模是最佳的。

+ **自带用于推理的基础映像**<br/> 另外，客户经常抱怨难以从试验转移到推理 RE 共享依赖项。 现在，使用新的基础映像共享功能，可以重复使用所有试验基础映像和依赖项进行推理。 这可以加快部署速度，并减少内部与外部循环的差距。

+ **改进了 Swagger 架构生成体验**<br/> 以前的 Swagger 生成方法容易出错，且无法自动化。 我们提供一种新的内联方法，用于通过修饰器从任何 Python 函数生成 Swagger 架构。 我们为此提供开源的代码，架构生成协议不会耦合到 Azure ML 平台。

+ **Azure ML CLI 已推出正式版 (GA)**<br/> 现在，使用一条 CLI 命令即可部署模型。 客户经常反馈他们不会从 Jupyter 笔记本部署 ML 模型。 [**CLI 参考文档**](https://aka.ms/azmlcli)已更新。


## <a name="2019-04-22"></a>2019-04-22

已发布适用于 Python 的 Azure 机器学习 SDK v1.0.30。

引入了 [`PipelineEndpoint`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline_endpoint.pipelineendpoint?view=azure-ml-py)，以便在维护同一终结点时添加已发布管道的新版本。

## <a name="2019-04-15"></a>2019-04-15

### <a name="azure-portal"></a>Azure 门户
  + 现在可以在现有的远程计算群集上重新提交现有的脚本运行。
  + 现在可以在“管道”选项卡上使用新参数运行已发布的管道。
  + 运行详细信息现在支持新的快照文件查看器。 提交特定的运行时，可以查看目录的快照。 还可以下载已提交用于启动运行的笔记本。
  + 现在可以从 Azure 门户取消父运行。

## <a name="2019-04-08"></a>2019-04-08

### <a name="azure-machine-learning-sdk-for-python-v1023"></a>适用于 Python 的 Azure 机器学习 SDK v1.0.23

+ **新功能**
  + Azure 机器学习 SDK 现在支持 Python 3.7。
  + Azure 机器学习 DNN 评估器现在提供内置的多版本支持。 例如，`TensorFlow` 评估器现在接受 `framework_version` 参数，用户可以指定版本“1.10”或“1.12”。 如需当前 SDK 发行版支持的版本列表，请对所需的框架类调用 `get_supported_versions()`（例如 `TensorFlow.get_supported_versions()`）。
  有关最新 SDK 发行版支持的版本列表，请参阅 [DNN 评估器文档](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn?view=azure-ml-py)。

## <a name="2019-03-25"></a>2019-03-25

### <a name="azure-machine-learning-sdk-for-python-v1021"></a>适用于 Python 的 Azure 机器学习 SDK v1.0.21

+ **新功能**
  + 使用 *azureml.core.Run.create_children* 方法可以通过单次调用以较低的延迟创建多个子运行。

## <a name="2019-03-11"></a>2019-03-11

### <a name="azure-machine-learning-sdk-for-python-v1018"></a>适用于 Python 的 Azure 机器学习 SDK v1.0.18

 + **更改**
   + azureml-tensorboard 包取代了 azureml-contrib-tensorboard。
   + 在此版本中，可以在创建托管计算群集 (amlcompute) 时在其上设置用户帐户。 可以通过在预配配置中传递这些属性来实现此目的。 可以在 [SDK 参考文档](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--)中找到更多详细信息。

### <a name="azure-machine-learning-data-prep-sdk-v1017"></a>Azure 机器学习数据准备 SDK v1.0.17

+ **新功能**
  + 现在支持使用表达式语言添加两个数字列来生成结果列。

+ **Bug 修复与改进**
  + 改进了 random_split 的文档和参数检查。

## <a name="2019-02-27"></a>2019-02-27

### <a name="azure-machine-learning-data-prep-sdk-v1016"></a>Azure 机器学习数据准备 SDK v1.0.16

+ **Bug 修复**
  + 修复了 API 更改导致的服务主体身份验证问题。

## <a name="2019-02-25"></a>2019-02-25

### <a name="azure-machine-learning-sdk-for-python-v1017"></a>适用于 Python 的 Azure 机器学习 SDK v1.0.17

+ **新功能**

  + Azure 机器学习现在为流行的 DNN 框架 Chainer 提供一流的支持。 用户可以使用 [`Chainer`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) 类轻松训练和部署 Chainer 模型。
    + 了解如何[使用 ChainerMN 运行分布式训练](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/chainer/training/distributed-chainer/distributed-chainer.ipynb)
    + 了解如何[使用 HyperDrive 通过 Chainer 运行超参数优化](https://github.com/Azure/MachineLearningNotebooks/blob/b881f78e4658b4e102a72b78dbd2129c24506980/how-to-use-azureml/ml-frameworks/chainer/deployment/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb)
  + Azure 机器学习管道添加了根据数据存储修改触发管道运行的功能。 管道[计划笔记本](https://aka.ms/pl-schedule)已更新，以展示此功能。

+ **Bug 修复与改进**
  + 我们已在 Azure 机器学习管道中添加了支持，以便在提供给 [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py) 的 [RunConfigurations](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) 中，将 source_directory_data_store 属性设置为所需的数据存储（例如 Blob 存储）。 默认情况下，步骤使用 Azure 文件存储作为后备数据存储，以并行方式执行大量步骤，这可能会遇到限制问题。

### <a name="azure-portal"></a>Azure 门户

+ **新功能**
  + 为报告添加了新的拖放式表编辑器体验。 用户可将列从井拖放到表区域，其中会显示表的预览。 可以重新排列这些列。
  + 新的日志文件查看器
  + 从“活动”选项卡链接到试验运行、计算、模型、映像和部署

## <a name="next-steps"></a>后续步骤

阅读 [Azure 机器学习](overview-what-is-azure-ml.md)的概述。
