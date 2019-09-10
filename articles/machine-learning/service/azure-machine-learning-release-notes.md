---
title: 版本新增内容
titleSuffix: Azure Machine Learning service
description: 了解 Azure 机器学习服务的最新更新以及机器学习和数据准备 Python SDK。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: jmartens
author: j-martens
ms.date: 08/19/2019
ms.custom: seodec18
ms.openlocfilehash: 78d16e8e6fc43644cdb318f8e402c2e8bbe0363e
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772495"
---
# <a name="azure-machine-learning-service-release-notes"></a>Azure 机器学习服务发行说明

本文介绍 Azure 机器学习服务版本。  有关完整的 SDK 参考内容，请访问 Azure 机器学习的[**主要 sdk For Python**](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)参考页。 

请参阅[已知问题列表](resource-known-issues.md)了解已知 bug 和解决方法。

## <a name="2019-09-03"></a>2019-09-03
### <a name="azure-machine-learning-sdk-for-python-v1060"></a>用于 Python 的 Azure 机器学习 SDK 1.0.60

+ **新功能**
  + 引入了 FileDataset，它引用数据存储或公用 url 中的单个或多个文件。 文件可以是任何格式。 FileDataset 使你能够将文件下载或装载到你的计算中。 若要了解 FileDataset，请访问 https://aka.ms/file-dataset 。
  + 添加了管道 Yaml 支持，PythonScript 步骤，Adla 步骤，Databrick 步骤，DataTransferStep 和 AzureBatch 步骤

+ **Bug 修复和改进**
  + **automl-核心**
    + AutoArima 现在为仅用于预览的 suggestable 管道。
    + 改进了预测的错误报告。
    + 通过在预测任务中使用自定义异常而不是泛型来改进日志记录。
    + 已删除对 max_concurrent_iterations 的检查，使其小于迭代总数。
    + AutoML 模型现在返回 AutoMLExceptions
    + 此版本提高了自动机器学习本地运行的执行性能。
  + **azureml-core**
    + 引入`Dataset.get_all()` ，它返回由其`TabularDataset`注册`FileDataset`名称键控的和对象的字典。 
    
    ```py 
    workspace = Workspace.from_config() 
    all_datasets = Dataset.get_all(workspace) 
    mydata = all_datasets['my-data'] 
    ```
    
    + 引入`parition_format` `Dataset.Tabular.from_delimited_files`和作为`Dataset.Tabular.from_parquet.files`参数。 每个数据路径的分区信息将基于指定的格式提取到列中。 "{column_name}" 创建字符串列，"{column_name： yyyy/MM/dd/HH/MM/ss}" 创建 datetime 列，其中，"yyyy"、"MM"、"dd"、"HH"、"mm" 和 "ss" 用于提取 datetime 类型的年、月、日、小时、分钟和秒。 Partition_format 应从第一个分区键的位置开始，直到文件路径结束。 例如，给定路径 "../USA/2019/01/01/data.csv ' 其中，分区是按国家/地区和时间，partition_format = '/{Country}/{PartitionDate： yyyy/MM/dd}/data .csv ' 创建值为 "USA"、日期时间列为 "PartitionDate" 且值为 "2019-01-01" 的字符串列 "Country"。
    + `to_csv_files`和`to_parquet_files`方法已添加到`TabularDataset`。 这些方法`FileDataset`通过将数据转换`TabularDataset`为指定格式的文件来实现与之间的转换。
    + 保存由 Model （）生成的 Dockerfile 时，自动登录到基本映像注册表。
    + 不再需要 "gpu_support";AzureML 现在会自动检测并使用 nvidia docker 扩展（如果可用）。 它将在未来版本中删除。
    + 添加了对创建、更新和使用 PipelineDrafts 的支持。
    + 此版本提高了自动机器学习本地运行的执行性能。
    + 用户可以按名称从运行历史记录查询指标。
    + 通过在预测任务中使用自定义异常而不是泛型来改进日志记录。
  + **azureml-explain-model**
    + 已将 feature_maps 参数添加到新的 MimicWrapper，允许用户获取原始功能说明。
    + 数据集上载现在默认情况下处于关闭状态以进行解释上载，并可使用 upload_datasets = True 重新启用。
    + 添加了 "is_law" 筛选参数以说明列表和下载功能。
    + 将方法`get_raw_explanation(feature_maps)`添加到全局和本地说明对象。
    + 向 lightgbm 添加了版本检查（如果低于支持的版本）
    + 批处理解释时的优化内存使用情况
    + AutoML 模型现在返回 AutoMLExceptions
  + **azureml-pipeline-core**
    + 添加了对 create、update 和 use PipelineDrafts 的支持-可用于维护可变管道定义并以交互方式使用它们来运行
  + **azureml-train-automl**
    + 用于安装特定版本的支持 gpu 的 pytorch v 1.1.0、cuda 工具包9.0、pytorch 转换器的功能，在远程 python 运行时环境中启用经理 BERT/XLNet 需要此功能。
  + **azureml-train-core**
    + 在 sdk 中直接发生某些超参数空间定义错误，而不是服务器端。

### <a name="azure-machine-learning-data-prep-sdk-v1114"></a>Azure 机器学习数据准备 SDK 1.1.14
+ **Bug 修复和改进**
  + 已启用使用原始路径和凭据写入 ADLS/ADLSGen2。
  + 修复了导致`include_path=True`不起作用的`read_parquet`bug。
  + 修复`to_pandas_dataframe()`了 "属性值无效： hostSecret" 异常导致的失败。
  + 修复了在 Spark 模式下无法在 DBFS 上读取文件的 bug。
  
## <a name="2019-08-19"></a>2019-08-19

### <a name="azure-machine-learning-sdk-for-python-v1057"></a>用于 Python 的 Azure 机器学习 SDK 1.0.57
+ **新功能**
  + 已`TabularDataset`启用以供 AutomatedML 使用。 若要了解有关`TabularDataset`的详细信息 https://aka.ms/azureml/howto/createdatasets ，请访问。
  
+ **Bug 修复和改进**
  + **automl-nativeclient-ios**
    + 修复了在以 pandas 数据帧但不是 numpy 数组形式提供训练和/或验证标签（y 和 y_valid）时引发的错误。
    + 将创建的`RawDataContext`接口更新为仅需要数据`AutoMLBaseSettings`和对象。
    +  允许 AutoML 用户删除在预测时不够长的培训系列。 -允许 AutoML 用户从预测集中不存在的测试集中删除粒度。
  + **azure-cli-ml**
    + 你现在可以为在 AKS 群集上部署的用于 Microsoft 生成的评分终结点和客户证书更新 SSL 证书。
  + **automl-核心**
    + 修复了 AutoML 中缺少标签的行未正确删除的问题。
    + 改进了 AutoML 中的错误日志记录;现在，完整的错误消息始终写入日志文件。
    + AutoML 已将其包固定更新为`azureml-defaults`包括`azureml-explain-model`、和`azureml-dataprep`。 AutoML 将不再对包不匹配（ `azureml-train-automl`包除外）发出警告。
    + 修复了 timeseries 中 cv 拆分的大小不相等的问题，导致箱计算失败。
    + 在运行交叉验证定型类型的系综迭代时，如果我们最终无法下载针对整个数据集训练的模型，则模型权重与正在送入投票的模型之间存在不一致系综.
    + 修复了在以 pandas 数据帧但不是 numpy 数组形式提供训练和/或验证标签（y 和 y_valid）时引发的错误。
    + 修复了在输入表的布尔值列中没有遇到任何内容时预测任务的问题。
    + 允许 AutoML 用户删除在预测时不够长的培训系列。 -允许 AutoML 用户从预测集中不存在的测试集中删除粒度。
  + **azureml-core**
    + 修复了 blob_cache_timeout 参数排序问题。
    + 向系统错误添加了外部的 "匹配" 和 "转换" 异常类型。
    + 添加了对 Key Vault 的远程运行机密的支持。 添加 keyvault 类以添加、获取和列出与工作区关联的 keyvault 中的机密。 支持的操作包括：
      + azureml. get _default_keyvault （）
      + keyvault. Keyvault _secret （name，value）
      + keyvault. Keyvault _secrets （secrets_dict）
      + keyvault. Keyvault _secret （name）
      + keyvault. Keyvault _secrets （secrets_list）
      + keyvault. Keyvault. list_secrets （）
    + 用于获取默认 keyvault 并在远程运行期间获得机密的其他方法：
      + azureml. get _default_keyvault （）
      + azureml. 运行. get _secret （名称）
      + azureml. _secrets （secrets_list）
    + 将其他替代参数添加到了 hyperdrive CLI 命令。
    + 提高 API 调用的可靠性会将重试次数扩展到常见的请求库异常。
    + 添加了对从已提交的运行提交运行的支持。
    + 修复了 FileWatcher 中的过期 SAS 令牌问题，这会导致文件在其初始令牌过期后停止上传。
    + 支持在数据集 python SDK 中导入 HTTP csv/tsv 文件。
    + 弃用了 Workspace （）方法。 显示给用户的警告消息建议使用 create （）或 get （）/from_config （）。
    + 添加了环境。添加了 _private_pip_wheel （），它允许将专用自定义 python 包（. whl）上传到工作区，并安全地使用它们来生成/具体化环境。
    + 你现在可以为在 AKS 群集上部署的用于 Microsoft 生成的评分终结点和客户证书更新 SSL 证书。
  + **azureml-explain-model**
    + 添加了参数，用于将模型 ID 添加到上传说明。
    + 在`is_raw`内存中添加了标记并将其上传。
    + 添加了 azureml-说明模型包的 pytorch 支持和测试。
  + **azureml-opendatasets**
    + 支持检测和记录自动测试环境。
    + 添加了类，使我们通过县和 zip 进行填充。
  + **azureml-pipeline-core**
    + 为输入和输出端口定义添加了标签属性。
  + **azureml-遥测**
    + 修复了不正确的遥测配置。
  + **azureml-train-automl**
    + 修复了在设置失败的情况下，没有为安装程序运行记录 "错误" 字段，因此未将其存储在父运行 "错误" 中。
    + 修复了 AutoML 中缺少标签的行未正确删除的问题。
    + 允许 AutoML 用户删除在预测时不够长的培训系列。
    + 允许 AutoML 用户从预测集中不存在的测试集中删除粒度。
    + 现在，AutoMLStep 将通过 automl config 传递到后端，以避免发生更改或添加新的配置参数的任何问题。
    + AutoML Data Guardrail 现为公共预览版。 在定型后，用户将看到一个数据 Guardrail 报告（用于分类/回归任务），并且还可以通过 SDK API 访问它。
  + **azureml-train-core**
    + 在 PyTorch 估计器中添加了 torch 1.2 支持。
  + **azureml-小组件**
    + 改进了分类培训的混淆矩阵图。

### <a name="azure-machine-learning-data-prep-sdk-v1112"></a>Azure 机器学习数据准备 SDK 1.1.12
+ **新功能**
  + 现在可将字符串列表作为输入`read_*`传递给方法。

+ **Bug 修复和改进**
  + 在 Spark 中`read_parquet`运行时，的性能得到了显著改进。
  + 修复了在具有`column_type_builder`不明确日期格式的单个列的情况下失败的问题。

### <a name="azure-portal"></a>Azure 门户
+ **预览功能**
  + 现在可以在 "运行详细信息" 页上使用日志和输出文件流。 当启用了预览切换功能时，这些文件将实时流更新。
  + 在工作区级别设置配额的功能在预览中发布。 AmlCompute 配额在订阅级别分配，但我们现在允许你在工作区之间分配该配额，并将其分配给公平共享和管理。 只需单击工作区左侧导航栏中的 "**使用情况 + 配额**" 边栏选项卡，然后选择 "**配置配额**" 选项卡。请注意，你必须是订阅管理员才能在工作区级别设置配额，因为这是一个跨工作区操作。

## <a name="2019-08-05"></a>2019-08-05

### <a name="azure-machine-learning-sdk-for-python-v1055"></a>用于 Python 的 Azure 机器学习 SDK 1.0.55

+ **新功能**
  + 对于在 AKS 上部署的评分终结点进行的调用，现在支持基于令牌的身份验证。 我们将继续支持当前的基于密钥的身份验证，用户可以一次使用其中一种身份验证机制。
  + 能够将虚拟网络（VNet）后面的 blob 存储注册为数据存储。
  
+ **Bug 修复和改进**
  + **automl-核心**
    + 修复了 CV 拆分的验证大小较小的 bug，并产生了用于回归和预测的错误预测与真实图表。
    + 远程运行的预测任务日志记录已改进，现在用户在运行失败时提供全面的错误消息。
    + 如果预处理标志为 True，则修复了 Timeseries 故障。
    + 做出一些预测数据验证错误消息。
    + 通过删除和/或延迟加载数据集（特别是在进程生成之间），减少了 AutoML 运行的内存消耗
  + **contrib-说明-模型**
    + 向 explainers 添加了 model_task 标志，以允许用户重写模型类型的默认自动推理逻辑
    + 小组件更改：使用 contrib 自动安装，无需更多 nbextension 安装/启用-支持说明，仅具有全局功能重要性（例如 Permutative）
    + 面板更改：-Box 图形和 violin 图除了 "摘要" 页上的 "beeswarm" 绘图外，还会在 "上 k" 滑块更改上以更快的速度 rerendering beeswarm 绘图未提供数据
  + **azureml-core**
    + 添加了 Model （）方法来创建 Docker 映像和封装模型及其依赖项的 Dockerfile。
    + 更新了本地 webservices 以接受包含环境对象的 InferenceConfigs。
    + 修复了在 "."（适用于当前目录）将作为 model_path 参数进行传递。
    + 添加 submit_child，该功能会镜像试验。在将运行指定为提交的子运行的父项时提交。
    + 支持 register_model 中的模型的配置选项。
    + 能够在现有群集上运行 JAR 作业。
    + 现在支持 instance_pool_id 和 cluster_log_dbfs_path 参数。
    + 添加了对在将模型部署到 Webservice 时使用环境对象的支持。 现在，环境对象可以作为 InferenceConfig 对象的一部分提供。
    + 为新区域添加 appinsifht 映射-centralus-westus-northcentralus
    + 为所有数据存储类中的所有属性添加了文档。
    + 已将 blob_cache_timeout 参数`Datastore.register_azure_blob_container`添加到。
    + 将 save_to_directory 和 load_from_directory 方法添加到 azureml。
    + 向 CLI 添加了 "az ml 环境下载" 和 "az ml 环境 register" 命令。
    + 添加了环境。添加 _private_pip_wheel 方法。
  + **azureml-explain-model**
    + 使用数据集服务（预览版）将数据集跟踪添加到说明中。
    + 将全局说明从10k 流式传输到100时，减少了默认的批大小。
    + 向 explainers 添加了 model_task 标志，以允许用户重写模型类型的默认自动推理逻辑。
  + **azureml-mlflow**
    + 修复了在 mlflow 中忽略了嵌套目录的 build_image 中的 bug。
  + **azureml-pipeline-steps**
    + 添加了在现有 Azure Databricks 群集上运行 JAR 作业的功能。
    + 添加了 DatabricksStep 步骤的支持 instance_pool_id 和 cluster_log_dbfs_path 参数。
    + 添加了对 DatabricksStep 步骤中管道参数的支持。
  + **azureml-train-automl**
    + 添加了系综相关文件的 docstrings。
    + 针对和的更合适的语言`max_cores_per_iteration`更新了文档`max_concurrent_iterations`
    + 远程运行的预测任务日志记录已改进，现在用户在运行失败时提供全面的错误消息。
    + 已从管道 automlstep 笔记本中删除 get_data。
    + 已开始支持 automlstep 中的 dataprep。

### <a name="azure-machine-learning-data-prep-sdk-v1110"></a>Azure 机器学习数据准备 SDK 1.1.10

+ **新功能**
  + 你现在可以请求对特定列执行特定检查器（例如直方图、散点图等）。
  + 向`append_columns`添加了一个并行化参数。 如果为 True，则数据将加载到内存中，但执行将并行运行;如果为 False，则执行将进行流式处理但单线程。

## <a name="2019-07-23"></a>2019-07-23

### <a name="azure-machine-learning-sdk-for-python-v1053"></a>用于 Python 的 Azure 机器学习 SDK 1.0.53

+ **新功能**
  + 自动机器学习现在支持远程计算目标上的 ONNX 模型定型
  + Azure 机器学习现在可以从以前的运行、检查点或模型文件恢复培训。
    + 了解如何[使用估算从上一次运行中恢复培训](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-tensorflow-resume-training/train-tensorflow-resume-training.ipynb)

+ **Bug 修复和改进**
  + **automl-nativeclient-ios**
    + 修复有关转换后丢失列类型的 bug （链接错误）; 
    + 允许 y_query 为一个对象类型，该对象类型在开头（#459519）不包含任何内容。
  + **azure-cli-ml**
    + CLI 命令 "模型部署" 和 "服务更新" 现在接受参数、配置文件或二者的组合。 参数优先于文件中的属性。
    + 现在，可以在注册后更新模型说明
  + **automl-核心**
    + 将 NimbusML 依赖关系更新到1.2.0 版本（当前最新版本）。
    + 添加了对 Nimbus ML 估算 & 要在 AutoML 估算中使用的管道的支持。
    + 修复系综选择过程中的 bug，该过程不必要地增加生成的系综，即使分数保持不变也是如此。
    + 允许跨 CV 分割重复使用某些 featurizations 以实现预测任务。 这会加速运行时运行时的运行时间，这是一种 n_cross_validations 的昂贵 featurizations，如滞后时间和滚动窗口。
    + 解决 pandas 支持的时间范围内的时间问题。 如果时间小于 pd，我们现在会引发 DataException。Timestamp。 min 或大于 pd。Timestamp。 max
    + 预测现在允许在定型和测试集中实现不同的频率，如果它们可以对齐。 例如，"从一月开始的季度" 和 "10 月开始的季度" 可以对齐。
    + "Parameters" 属性已添加到 TimeSeriesTransformer 中。
    + 删除旧的异常类。
    + 在预测任务中， `target_lags`参数现在接受单个整数值或整数列表。 如果提供了整数，则只会创建一个 lag。 如果提供了列表，则会采用滞后时间的唯一值。 target_lags = [1，2，2，4] 将创建1、2和4个周期的滞后时间。
    + 修复有关转换后丢失列类型的 bug （链接错误）;
    + 在`model.forecast(X, y_query)`中，允许 y_query 为在开头（#459519）不包含任何内容的对象类型。
    + 向 automl 输出添加预期值
  + **contrib-datadrift**
    +  示例笔记本的改进，包括切换到 azureml-opendatasets 而不是 contrib-opendatasets 和丰富数据时的性能改进
  + **contrib-说明-模型**
    + 固定的转换参数，适用于说明的原始功能重要性-contrib--说明-型号包
    + 已将 segmentations 添加到说明中的图像解释
    + 添加 LimeExplainer 的 scipy 稀疏支持
    + 将 batch_size 添加到模拟说明的 include_local = False，以便对批处理中的全局说明进行流式处理以提高 DecisionTreeExplainableModel 的执行时间
  + **contrib-featureengineering**
    + 用于调用 set_featurizer_timeseries_params （）的修复： dict 值类型更改和 null 检查-添加笔记本 for timeseries 特征化器
    + 将 NimbusML 依赖关系更新到1.2.0 版本（当前最新版本）。
  + **azureml-core**
    + 添加了在 AzureML CLI 中附加 DBFS 数据存储的功能 
    + 修复了数据存储上传的 bug，其中创建了一个`target_path`空文件夹（如果在`/`
    + 修复了 ServicePrincipalAuthentication 中的 deepcopy 问题。
    + 向 CLI 添加了 "az ml 环境 show" 和 "az ml 环境 list" 命令。
    + 现在，环境支持指定 base_dockerfile 作为已生成的 base_image 的替代项。
    + 未使用的 RunConfiguration 设置 auto_prepare_environment 已标记为已弃用。
    + 现在，可以在注册后更新模型说明
    + Bug 修复模型和图像删除现在提供了有关检索上游对象的详细信息，如果由于上游依赖关系而删除操作失败，则会依赖于这些对象。
    + 修复了在为某些环境创建工作区时发生的部署的空白持续时间的 bug。
    + 改善了工作区创建失败异常。 这样，用户将看不到 "无法创建工作区"。 找不到 ... "作为消息，而是查看实际的创建失败。
    + 在 AKS webservices 中添加对令牌身份验证的支持。 
    + 向`get_token()` `Webservice`对象中添加方法。
    + 添加了用于管理机器学习数据集的 CLI 支持。
    + `Datastore.register_azure_blob_container`现在可以选择使用`blob_cache_timeout`一个值（以秒为单位）来配置 blobfuse 的 mount 参数，以便为此数据存储启用缓存过期。 默认值为无超时值，即当读取 blob 时，它将保留在本地缓存中，直到作业完成。 大多数作业将优先使用此设置，但某些作业需要从大型数据集读取超过其节点的更多数据。 对于这些作业，优化此参数将有助于它们成功。 优化此参数时请小心：将值设置得过低可能会导致性能不佳，因为在 epoch 中使用的数据可能会在再次使用之前过期。 这意味着将从 blob 存储（即网络）而不是本地缓存中完成所有读取，这会对定型时间产生负面影响。
    + 现在，可以在注册后正确更新模型说明
    + 模型和图像删除现在提供了有关依赖于它们的上游对象的详细信息，导致删除失败
    + 使用 mlflow 提高远程运行的资源利用率。
  + **azureml-explain-model**
    + 固定的转换参数，适用于说明的原始功能重要性-contrib--说明-型号包
    + 添加 LimeExplainer 的 scipy 稀疏支持
    + 添加了 shap 线性说明包装，并向表格说明添加了另一个级别用于说明线性模型
    + 对于 "说明模型库" 中的模拟说明，修复了 include_local = False 进行稀疏数据输入时的错误
    + 向 automl 输出添加预期值
    + 修复了在为获取原始功能重要性而提供的转换参数时的排列功能重要性
    + 将 batch_size 添加到模拟说明的 include_local = False，以便对批处理中的全局说明进行流式处理以提高 DecisionTreeExplainableModel 的执行时间
    + 对于模型 explainability 库，修复了黑盒 explainers，其中 pandas 数据帧输入需要进行预测
    + 修复了一个 bug `explanation.expected_values` ，在这种情况下，有时会返回 float 而不是其中包含 float 的列表。
  + **azureml-mlflow**
    + 提高 mlflow 的性能 _experiment （experiment_name）
    + 修复用于 mlflow tracking_uri 的 InteractiveLoginAuthentication 的 bug
    + 使用 mlflow 提高远程运行的资源利用率。
    + 改进 mlflow 包的文档
    + 修补程序 bug，其中，mlflow _artifacts （"my_dir"）会将项目保存在 "my_dir/< 项目-路径 >" 下，而不是 "< 项目路径 >"
  + **azureml-opendatasets**
    + 由于新引入的内存问题，将 opendatasets 的 pyarrow 固定到旧版本（< 0.14.0）。
    +  将 contrib-opendatasets 移动到 azureml-opendatasets。 -允许将打开的数据集类注册到 AML 工作区，并无缝地利用 AML Dataset 功能。 -显著提高非 SPARK 版本中 NoaaIsdWeather 的性能。
  + **azureml-pipeline-steps**
    + DBFS 数据存储现在支持 DatabricksStep 中的输入和输出。
    + 更新了有关输入/输出的 Azure Batch 步骤的文档。
    + 在 AzureBatchStep 中，将*delete_batch_job_after_finish*默认值更改为*true*。
  + **azureml-遥测**
    +  将 contrib-opendatasets 移动到 azureml-opendatasets。 -允许将打开的数据集类注册到 AML 工作区，并无缝地利用 AML Dataset 功能。 -显著提高非 SPARK 版本中 NoaaIsdWeather 的性能。
  + **azureml-train-automl**
    + 更新了有关 get_output 的文档，以反映实际的返回类型，并提供有关检索密钥属性的其他说明。
    + 将 NimbusML 依赖关系更新到1.2.0 版本（当前最新版本）。
    + 向 automl 输出添加预期值
  + **azureml-train-core**
    + 现在可将字符串作为自动超参数优化的计算目标接受
    + 未使用的 RunConfiguration 设置 auto_prepare_environment 已标记为已弃用。

### <a name="azure-machine-learning-data-prep-sdk-v119"></a>Azure 机器学习数据准备 SDK 1.1。9

+ **新功能**
  + 添加了对从 http 或 https url 直接读取文件的支持。

+ **Bug 修复和改进**
  + 当尝试从远程源（当前不支持）读取 Parquet 数据集时，改进了错误消息。
  + 修复了在 ADLS 第2代中写入 Parquet 文件格式的 bug，并更新了路径中的 ADLS 第2代容器名称。

## <a name="2019-07-09"></a>2019-07-09

### <a name="visual-interface"></a>视觉对象接口
+ **预览功能**
  + 在可视界面中添加了 "执行 R 脚本" 模块。

### <a name="azure-machine-learning-sdk-for-python-v1048"></a>用于 Python 的 Azure 机器学习 SDK 1.0.48

+ **新功能**
  + **azureml-opendatasets**
    + **contrib-opendatasets**现在作为**azureml-opendatasets**提供。 旧包仍可正常工作，但建议使用**opendatasets** ，以获得更丰富的功能和改进。
    + 此新包允许你将打开的数据集注册为 AML 工作区中的数据集，并利用数据集提供的任何功能。
    + 它还包括一些现有功能，如将打开的数据集用作 Pandas/SPARK dataframes 以及某些数据集（如天气）的位置联接。

+ **预览功能**
    + HyperDriveConfig 现在可以接受管道对象作为参数，以支持使用管道进行超参数优化。

+ **Bug 修复和改进**
  + **azureml-train-automl**
    + 修复了转换后丢失列类型的相关 bug。
    + 修复了 bug，以允许 y_query 是一个不包含任何内容的对象类型。 
    + 修复了系综选择过程中不必要地增加生成的系综的问题，即使分数保持不变也是如此。
    + 解决了 AutoMLStep 中 whitelist_models 和 blacklist_models 设置的问题。
    + 修复了在 Azure ML 管道的上下文中使用 AutoML 时阻止使用预处理的问题。
  + **azureml-opendatasets**
    + 已将 contrib-opendatasets 移动到 azureml-opendatasets。
    + 允许打开的数据集类注册到 AML 工作区并无缝地利用 AML 数据集功能。
    + 显著改进了非 SPARK 版本中的 NoaaIsdWeather 丰富性能。
  + **azureml-explain-model**
    + 更新了 interpretability 对象的联机文档。
    + 将 batch_size 添加到模拟说明的 include_local = False，以便对批处理中的全局说明进行流式处理以提高 DecisionTreeExplainableModel 的执行时间。
    + 修复了这样的`explanation.expected_values`问题：有时会返回 float，而不是其中包含 float 的列表。
    + 向说明模型库中的模拟说明的 automl 输出添加了预期值。
    + 修复了在为获取原始功能重要性而提供的转换参数时的排列功能重要性。
    + 将 batch_size 添加到模拟说明的 include_local = False，以便对批处理中的全局说明进行流式处理，以提高模型 explainability 库的 DecisionTreeExplainableModel 的执行时间。
  + **azureml-core**
    + 添加了在 AzureML CLI 中附加 DBFS 数据存储的功能。
    + 修复了数据存储上传的问题，其中创建了一个`target_path`空文件夹`/`（如果使用开始）。
    + 启用了两个数据集的比较。
    + 模型和图像删除现在提供了有关检索上游对象的详细信息，如果由于上游依赖关系而删除操作失败，则会依赖于这些对象。
    + 弃用了 auto_prepare_environment 中未使用的 RunConfiguration 设置。
  + **azureml-mlflow**
    + 提高了使用 mlflow 的远程运行的资源利用率。
    + 改进了 mlflow 包的文档。
    + 修复了 mlflow _artifacts （"my_dir"）在 "my_dir/伪像-路径" 而不是 "项目路径" 下保存项目的问题。
  + **azureml-pipeline-core**
    + 所有管道步骤的参数 hash_paths 已弃用，并将在将来删除。 默认情况下，source_directory 的内容进行哈希处理（amlignore 或 .gitignore 中列出的文件除外）
    + 继续改进 Module 和 ModuleStep 以支持计算类型特定模块，为 RunConfiguration 集成做好准备，并进一步更改以在管道中对其使用进行解锁。
  + **azureml-pipeline-steps**
    + AzureBatchStep:有关输入/输出的改进文档。
    + AzureBatchStep:将 delete_batch_job_after_finish 默认值更改为 true。
  + **azureml-train-core**
    + 现在可将字符串作为自动超参数优化的计算目标接受。
    + 弃用了 auto_prepare_environment 中未使用的 RunConfiguration 设置。
    + 不推荐`conda_dependencies_file_path`使用`pip_requirements_file_path`的参数， `conda_dependencies_file`并`pip_requirements_file`分别为和。
  + **azureml-opendatasets**
    + 显著提高非 SPARK 版本中的 NoaaIsdWeather 丰富性能。

### <a name="azure-machine-learning-data-prep-sdk-v118"></a>Azure 机器学习数据准备 SDK 1.1。8

+ **新功能**
 + 现在可以循环访问数据流对象，从而生成一系列记录。 请参阅的`Dataflow.to_record_iterator`文档。

+ **Bug 修复和改进**
 + 增强了 DataPrep SDK 的可靠性。
 + 改进了 pandas DataFrames 与非字符串列索引的处理。
 + 提高了数据集`to_pandas_dataframe`的性能。
 + 修复了在多节点环境中运行时，Spark 执行数据集失败的 bug。

## <a name="2019-07-01"></a>2019-07-01

### <a name="azure-machine-learning-data-prep-sdk-v117"></a>Azure 机器学习数据准备 SDK 1.1。7

我们恢复了改进性能的更改，因为这会导致某些使用 Azure Databricks 的客户出现问题。 如果 Azure Databricks 上遇到问题，可以使用以下方法之一升级到版本1.1.7：
1. 运行以下脚本进行升级：`%sh /home/ubuntu/databricks/python/bin/pip install azureml-dataprep==1.1.7`
2. 重新创建群集，这将安装最新的数据准备 SDK 版本。

## <a name="2019-06-25"></a>2019-06-25

### <a name="azure-machine-learning-sdk-for-python-v1045"></a>用于 Python 的 Azure 机器学习 SDK 1.0.45

+ **新功能**
  + 添加决策树代理项模型来模拟 azureml 中的说明包
  + 可以指定要安装在推断映像上的 CUDA 版本。 支持 CUDA 9.0、9.1 和10.0。
  + 有关 Azure ML 培训基础映像的信息现已在[AZURE Ml 容器 GitHub 存储库](https://github.com/Azure/AzureML-Containers)和[DockerHub](https://hub.docker.com/_/microsoft-azureml)中提供
  + 为管道计划添加了 CLI 支持。 运行 "az ml 漏斗-h" 以了解详细信息
  + 已将自定义 Kubernetes 命名空间参数添加到 AKS webservice 部署配置和 CLI。
  + 所有管道步骤的不推荐使用的 hash_paths 参数
  + 现在，注册支持将多个单独的文件注册为单个模型，并使用`child_paths`参数。
  
+ **预览功能**
    + 计分 explainers 现在可以选择保存 conda 和 pip 信息以实现更可靠的序列化和反序列化。
    + 自动功能选择器的 Bug 修复。
    + 已将 mlflow 更新为新的 api，并修复了新实现公开的 bug。

+ **Bug 修复和改进**
  + 从 azureml 核心中删除了 paramiko 依赖项。 为旧计算目标附加方法添加了弃用警告。
  + 提高运行的性能。 create_children
  + 在使用二元分类器模拟说明中，当教师概率用于缩放 shap 值时，修复概率顺序
  + 改善了自动机器学习的错误处理和消息。 
  + 解决了自动机器学习的迭代超时问题。
  + 提高了自动机器学习的时间序列转换性能。

## <a name="2019-06-24"></a>2019-06-24

### <a name="azure-machine-learning-data-prep-sdk-v116"></a>Azure 机器学习数据准备 SDK 1.1。6

+ **新功能**
  + 添加了 top 值（`SummaryFunction.TOPVALUES`）和下限值（`SummaryFunction.BOTTOMVALUES`）的汇总函数。

+ **Bug 修复和改进**
  + 显著提高了的性能`read_pandas_dataframe`。
  + 修复了一个 bug，该`get_profile()` bug 会导致指向二进制文件的数据流失败。
  + 公开`set_diagnostics_collection()`以允许对遥测集合进行编程启用/禁用。
  + 更改了的`get_profile()`行为。 现在，对于 Min、Mean、Std 和 Sum，NaN 值将被忽略，它们与 Pandas 的行为保持一致。


## <a name="2019-06-10"></a>2019-06-10

### <a name="azure-machine-learning-sdk-for-python-v1043"></a>用于 Python 的 Azure 机器学习 SDK 1.0.43

+ **新功能**
  + Azure 机器学习现在为常见机器学习和数据分析框架 Scikit-learn 提供一流支持。 使用估计器，用户可以轻松地训练和部署 scikit-learn 模型。 [ `SKLearn` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py)
    + 了解如何[使用 scikit-learn 运行超参数优化-了解如何使用 HyperDrive](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb)。
  + 添加了有关在管道中创建 ModuleStep 的支持，以及模块和 ModuleVersion 类，以管理可重用的计算单元。
  + ACI webservices 现在支持通过更新提供持久的 scoring_uri。 Scoring_uri 将从 IP 更改为 FQDN。 可以通过在 deploy_configuration 上设置 dns_name_label 来配置 FQDN 的 Dns 名称标签。 
  + 自动化机器学习的新功能：
    + STL 特征化器 for 预测
    + 为功能扫描启用了 KMeans 群集
  + AmlCompute 配额批准已变得更快！ 现在，我们已自动在阈值内批准配额请求。 有关配额工作方式的详细信息，请了解[如何管理配额](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-quotas)。

+ **预览功能**
    + 通过 MLflow 包（[例如笔记本](https://aka.ms/azureml-mlflow-examples)）与[MLflow](https://mlflow.org) 1.0.0 跟踪进行集成。
    + 提交 Jupyter 笔记本作为运行。 [API 参考文档](https://docs.microsoft.com/python/api/azureml-contrib-notebook/azureml.contrib.notebook?view=azure-ml-py)
    + 公共预览版通过 contrib-datadrift 包（[示例笔记本](https://aka.ms/azureml-datadrift-example)）的[数据偏移检测](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift?view=azure-ml-py)程序。 数据偏移是模型准确性随时间推移而降低的主要原因之一。 在生产环境中为模型提供的数据不同于对模型进行定型的数据时，会发生这种情况。 AML 数据偏移检测探测器可帮助客户监视数据偏移并在检测到偏差时发送警报。 

+ **重大更改**

+ **Bug 修复和改进**
  + RunConfiguration 加载和保存支持为以前的行为指定完整的文件路径，并具有完整的后向兼容性。
  + 在 ServicePrincipalAuthentication 中添加了缓存，默认情况下处于关闭状态。
  + 在同一指标名称下启用多个绘图的日志记录。
  + 现已正确地从 azureml （`from azureml.core import Model`）中导入模型类。
  + 在管道步骤中`hash_path` ，参数现已弃用。 新行为是对 source_directory （amlignore 或 .gitignore 中列出的文件除外）进行哈希处理。
  + 在管道包中， `get_all`已`get_all_*`不推荐使用和`list_*`的`list`各种和方法。
  + azureml。获取运行类型后，就不再需要导入类，然后返回原始的运行类型。
  + 修复了对 WebService 更新的某些调用未触发更新的问题。
  + AKS webservices 上的评分超时应介于5ms 和且之间。 评分请求的最大允许 scoring_timeout_ms 已从1分钟到5分钟的已升级。
  + LocalWebservice 对象现在具有`scoring_uri`和`swagger_uri`属性。
  + 已移动的输出目录创建和输出目录上传出用户进程。 已启用运行历史记录 SDK，以便在每个用户进程中运行。 这应该解决分布式培训运行遇到的一些同步问题。
  + 从用户进程名称写入的 azureml 日志的名称现在将包括进程名称（仅适用于分布式培训）和 PID。

### <a name="azure-machine-learning-data-prep-sdk-v115"></a>Azure 机器学习数据准备 SDK 1.1。5

+ **Bug 修复和改进**
  + 对于具有两位数年份格式的解释日期时间值，已更新有效年份范围，以匹配 Windows 可能会释放。 范围已从1930-2029 更改为1950-2049。
  + 在文件和设置`handleQuotedLineBreaks=True`中读取时， `\r`将被视为新行。
  + 修复了在某些情况`read_pandas_dataframe`下导致失败的 bug。
  + 改进的`get_profile`性能。
  + 改进的错误消息。

## <a name="2019-05-28"></a>2019-05-28

### <a name="azure-machine-learning-data-prep-sdk-v114"></a>Azure 机器学习数据准备 SDK 1.1。4

+ **新功能**
  + 现在可以使用以下表达式语言函数将日期时间值提取并分析到新列中。
    + `RegEx.extract_record()`将 datetime 元素提取到新列中。
    + `create_datetime()`从单独的 datetime 元素创建 datetime 对象。
  + 在调用`get_profile()`时，您现在可以看到分位列标记为（est.）以清楚地指示值是近似值。
  + 你现在可以在从 Azure Blob 存储中进行读取时使用 * * 组合。
    + 例如，`dprep.read_csv(path='https://yourblob.blob.core.windows.net/yourcontainer/**/data/*.csv')`

+ **Bug 修复**
  + 修复了与从远程源（Azure Blob）中读取 Parquet 文件相关的 bug。

## <a name="2019-05-14"></a>2019-05-14

### <a name="azure-machine-learning-sdk-for-python-v1039"></a>用于 Python 的 Azure 机器学习 SDK 1.0.39
+ **更改**
  + 正在弃用运行配置 auto_prepare_environment 选项，并将自动准备变为默认值。

## <a name="2019-05-08"></a>2019-05-08

### <a name="azure-machine-learning-data-prep-sdk-v113"></a>Azure 机器学习数据准备 SDK 1.1。3

+ **新功能**
  + 添加了对从 PostgresSQL 数据库读取的支持，方法是调用 read_postgresql 或使用数据存储。
    + 请参阅操作方法指南中的示例：
      + [数据引入笔记本](https://aka.ms/aml-data-prep-ingestion-nb)
      + [数据存储笔记本](https://aka.ms/aml-data-prep-datastore-nb)

+ **Bug 修复和改进**
  + 修复了列类型转换的问题：
  + 现在可将布尔值或数值列正确转换为布尔值列。
  + 现在，在尝试将日期列设置为日期类型时不会失败。
  + 改进了 JoinType 类型和随附的参考文档。 联接两个数据流时，你现在可以指定以下类型的联接之一：
    + NONE、MATCH、INNER、UNMATCHLEFT、LEFTANTI、LEFTOUTER、UNMATCHRIGHT、RIGHTANTI、RIGHTOUTER、FULLANTI、FULL。
  + 改进了数据类型推断以识别更多日期格式。

## <a name="2019-05-06"></a>2019-05-06

### <a name="azure-portal"></a>Azure 门户

在 Azure 门户中，现在可以：
+ 创建并运行自动 ML 试验 
+ 创建笔记本 VM，尝试使用示例 Jupyter 笔记本或自己的笔记本。
+ "机器学习服务" 工作区中的全新创作部分（预览），其中包括自动机器学习、视觉对象界面和托管笔记本 Vm
    + 使用自动机器学习自动创建模型 
    + 使用拖放可视界面运行试验
    + 创建笔记本 VM 来浏览数据、创建模型和部署服务。
+ 运行报表和运行详细信息页中的实时图表和指标更新
+ 更新了运行详细信息页中的日志、输出和快照的文件查看器。
+ "试验" 选项卡中新的和改进的报表创建体验。 
+ 添加了从 "Azure 机器学习服务" 工作区的 "概述" 页下载 .config 文件的功能。
+ 支持从 Azure Databricks 工作区创建机器学习服务工作区 

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033"></a>用于 Python 的 Azure 机器学习 SDK 1.0.33
+ **新功能**
  + _工作区。_ 现在，create 方法接受 CPU 和 GPU 群集的默认群集配置。
  + 如果工作区创建失败，则会清理依赖的资源。
  + 默认 Azure 容器注册表 SKU 切换为 "基本"。
  + Azure 容器注册表是在需要用于运行或创建映像时，惰式创建的。
  + 支持定型运行环境。

### <a name="notebook-virtual-machine"></a>笔记本虚拟机 

使用笔记本 VM 作为适用于 Jupyter 笔记本的安全、企业就绪的托管环境，你可以在其中计划机器学习试验，将模型部署为 web 终结点，并使用 Python 执行 Azure 机器学习 SDK 支持的所有其他操作。 它提供多种功能：
+ [快速启动预配置的笔记本 VM](tutorial-1st-experiment-sdk-setup.md) ，该 VM 具有最新版本的 Azure 机器学习 SDK 和相关包。
+ 通过 Azure Active Directory 身份验证和授权等经验证的技术（如 HTTPS）来保护访问。
+ Azure 机器学习工作区 blob 存储帐户中的笔记本和代码的可靠云存储。 可以安全地删除笔记本 VM，而不会丢失工作。
+ 预安装的示例笔记本，用于探索和试验 Azure 机器学习服务功能。
+ Azure Vm 的完整自定义功能、任何 VM 类型、任何包、任何驱动程序。 

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033-released"></a>Azure 机器学习 SDK for Python v 1.0.33 已发布。

+ [Fpga](concept-accelerate-with-fpgas.md)上的 Azure ML 硬件加速模型现已正式发布。
  + 你现在可以[使用加速-模型包](how-to-deploy-fpga-web-service.md)来执行以下操作：
    + 训练受支持的深层神经网络的权重（ResNet 50、ResNet 152、DenseNet-121、VGG-16 和 SSD-VGG）
    + 将传输学习与受支持的 DNN 配合使用
    + 向模型管理服务注册模型并容器化模型
    + 使用 Azure Kubernetes 服务（AKS）群集中的 FPGA 将模型部署到 Azure VM
  + 将容器部署到[Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview)服务器设备
  + 通过此[示例](https://github.com/Azure-Samples/aml-hardware-accelerated-models)将数据与 gRPC 终结点评分

### <a name="automated-machine-learning"></a>自动化机器学习

+ 功能扫描，可以动态添加 featurizers 以实现性能优化。 新 featurizers：工作嵌入、证据权重、目标编码、文本目标编码、群集距离
+ 用于处理自动 ML 内定型/有效拆分的智能 CV
+ 很少的内存优化更改和运行时性能改进
+ 模型说明的性能改进
+ 本地运行的 ONNX 模型转换
+ 增加了次采样支持
+ 智能停止，未定义退出条件
+ 堆积整体

+ 时序预测
  + 新建预测预测函数   
  + 你现在可以使用时序数据的滚动源交叉验证
  + 添加的新功能配置时序滞后 
  + 添加了新功能以支持滚动窗口聚合功能
  + 在实验设置中定义国家/地区代码时的新假日检测和特征化器

+ Azure Databricks
  + 启用时序预测和模型 explainabilty/interpretability 功能
  + 你现在可以取消和恢复（继续）自动 ML 试验
  + 添加了对多核处理的支持

### <a name="mlops"></a>MLOps
+ **用于计分容器 & 调试的本地部署**<br/> 现在，你可以在本地部署 ML 模型并快速循环访问评分文件和依赖项，以确保它们按预期方式运行。

+ **Introduced InferenceConfig & Model.deploy()**<br/> 模型部署现在支持使用条目脚本指定源文件夹，与 .Runconfig 相同。  此外，模型部署已简化为单个命令。

+ **Git 引用跟踪**<br/> 客户已请求使用基本 Git 集成功能，因为它有助于维持端到端的审核线索。 我们已经跨 Azure ML 中的主要实体实现了跨 Git 相关元数据（存储库、提交、清理状态）的跟踪。 此信息将由 SDK 和 CLI 自动收集。

+ **模型分析 & 验证服务**<br/> 客户经常会抱怨难以正确调整与其推理服务关联的计算的大小。 利用我们的模型分析服务，客户可以提供示例输入，我们将跨16个不同的 CPU/内存配置进行分析，以确定部署的最佳大小。

+ **自带用于推理的基本映像**<br/> 另一种常见的问题是，从试验到推理重新共享依赖项时遇到困难。 使用新的基本映像共享功能，现在可以重复使用试验基础映像、依赖项和所有项来进行推理。 这会加快部署速度，并减少从内部到外部循环的间隔。

+ **提高 Swagger 架构生成体验**<br/> 以前的 swagger 生成方法很容易出错，无法自动执行。 我们有一种新的基于修饰器的 Python 函数生成 swagger 架构的方式。 我们有开源的此代码，架构生成协议并不耦合到 Azure ML 平台。

+ **Azure ML CLI 现已正式发布（GA）**<br/> 现在可以使用单个 CLI 命令部署模型。 我们提供了一些常见的客户反馈，其中没有人从 Jupyter 笔记本部署 ML 模型。 [**CLI 参考文档**](https://aka.ms/azmlcli)已更新。


## <a name="2019-04-22"></a>2019-04-22

Azure 机器学习 SDK for Python v 1.0.30 已发布。

[`PipelineEndpoint`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline_endpoint.pipelineendpoint?view=azure-ml-py)介绍了如何在保持同一终结点的情况下添加新版本的已发布管道。

## <a name="2019-04-17"></a>2019-04-17

### <a name="azure-machine-learning-data-prep-sdk-v112"></a>Azure 机器学习数据准备 SDK 1.1。2

注意:数据准备 Python SDK 将不再安装`numpy`和`pandas`打包。 请参阅[更新的安装说明](https://aka.ms/aml-data-prep-installation)。

+ **新功能**
  + 现在可以使用透视转换。
    + 操作方法指南：[枢轴](https://aka.ms/aml-data-prep-pivot-nb)
  + 你现在可以在本机函数中使用正则表达式。
    + 例如：
      + `dflow.filter(dprep.RegEx('pattern').is_match(dflow['column_name']))`
      + `dflow.assert_value('column_name', dprep.RegEx('pattern').is_match(dprep.value))`
  + 你现在可以在`to_upper`表达式`to_lower`语言中使用 和 函数。
  + 现在，可以看到数据配置文件中每个列的唯一值数。
  + 对于一些常用的读取器步骤，你现在可以传入`infer_column_types`参数。 如果将其设置为`True`，则 "数据准备" 将尝试检测并自动转换列类型。
    + `inference_arguments` 现已弃用。
  + 你现在可以调用`Dataflow.shape`。

+ **Bug 修复和改进**
  + `keep_columns` 现在接受一个额外的可选`validate_column_exists`参数，该参数检查的`keep_columns`结果是否包含任何列。
  + 从文件读取的所有读取器步骤现在都接受其他可选参数`verify_exists`。
  + 提高了读取 pandas 数据帧和获取数据配置文件的性能。
  + 修复了这样的 bug：从数据流切分单个步骤失败，并出现单个索引。

## <a name="2019-04-15"></a>2019-04-15

### <a name="azure-portal"></a>Azure 门户
  + 你现在可以重新提交现有远程计算群集上运行的现有脚本。 
  + 你现在可以在 "管道" 选项卡上使用新参数运行已发布的管道。 
  + 运行详细信息现在支持新的快照文件查看器。 提交特定运行时，可以查看目录的快照。 你还可以下载已提交的笔记本以启动运行。
  + 你现在可以从 Azure 门户中取消父运行。

## <a name="2019-04-08"></a>2019-04-08

### <a name="azure-machine-learning-sdk-for-python-v1023"></a>用于 Python 的 Azure 机器学习 SDK 1.0.23

+ **新功能**
  + Azure 机器学习 SDK 现在支持 Python 3.7。
  + Azure 机器学习 DNN 估算现在提供内置的多版本支持。 例如， `TensorFlow`  估计器现在接受一个`framework_version`参数，并且用户可以指定版本 "1.10" 或 "1.12"。 有关当前 SDK 版本支持的版本的列表，请对所需`get_supported_versions()`的框架类调用（ `TensorFlow.get_supported_versions()`例如）。
  有关最新 SDK 版本支持的版本的列表，请参阅[DNN 估计器文档](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn?view=azure-ml-py)。

### <a name="azure-machine-learning-data-prep-sdk-v111"></a>Azure 机器学习数据准备 SDK 版本1.1。1

+ **新功能**
  + 可以使用 read_ * 转换来读取多个数据存储/数据路径/DataReference 源。
  + 您可以对列执行以下操作来创建新列：除法、楼层、取模、power、length。
  + 数据准备现在是 Azure ML 诊断套件的一部分，默认情况下会记录诊断信息。
    + 若要关闭此功能，请将此环境变量设置为 true：DISABLE_DPREP_LOGGER

+ **Bug 修复和改进**
  + 改进了常用类和函数的代码文档。
  + 修复了未能读取 Excel 文件的 auto_read_file 中的 bug。
  + 添加了覆盖 read_pandas_dataframe 中的文件夹的选项。
  + 提高了 dotnetcore2 依赖关系安装的性能，并增加了对 Fedora 27/28 和 Ubuntu 1804 的支持。
  + 提高了从 Azure Blob 读取的性能。
  + 列类型检测现在支持 Long 类型的列。
  + 修复了一些日期值显示为时间戳的 bug，而不是 Python datetime 对象。
  + 修复了某些类型计数显示为双精度型而不是整数的错误。

  
## <a name="2019-03-25"></a>2019-03-25

### <a name="azure-machine-learning-sdk-for-python-v1021"></a>用于 Python 的 Azure 机器学习 SDK 1.0.21

+ **新功能**
  + *Create_children*方法允许使用单个调用来创建多个子运行的低延迟。

### <a name="azure-machine-learning-data-prep-sdk-v110"></a>Azure 机器学习数据准备 SDK 1.1。0

+ **重大更改**
  + 数据准备包的概念已不推荐使用，不再受支持。 您可以单独保存数据流，而不是将多个数据流保存在一个包中。
    + 操作方法指南：[打开并保存数据流笔记本](https://aka.ms/aml-data-prep-open-save-dataflows-nb)

+ **新功能**
  + 数据准备现在可以识别与特定语义类型匹配的列，并相应地进行拆分。 当前支持的 STypes 包括：电子邮件地址、地理坐标（纬度 & 经度）、IPv4 和 IPv6 地址、美国电话号码和 US 邮政编码。
    + 操作方法指南：[语义类型笔记本](https://aka.ms/aml-data-prep-semantic-types-nb)
  + 数据准备现在支持以下操作，以便从两个数值列生成结果列：减、乘、除和模数。
  + 可以在数据流`verify_has_data()`上调用以检查数据流是否会在执行时产生记录。

+ **Bug 修复和改进**
  + 你现在可以在用于数值列配置文件的直方图中指定要使用的储箱数。
  + `read_pandas_dataframe`转换现在要求数据帧具有字符串或字节类型的列名称。
  + 修复了`fill_nulls`转换中的 bug，其中如果缺少列，则不会正确填充值。

## <a name="2019-03-11"></a>2019-03-11

### <a name="azure-machine-learning-sdk-for-python-v1018"></a>用于 Python 的 Azure 机器学习 SDK 1.0.18

 + **更改**
   + Tensorboard 包取代了 contrib-tensorboard。
   + 在此版本中，你可以在托管计算群集（amlcompute）上设置一个用户帐户，而创建该帐户。 可以通过在预配配置中传递这些属性来完成此操作。 可以在[SDK 参考文档](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none-)中找到更多详细信息。

### <a name="azure-machine-learning-data-prep-sdk-v1017"></a>Azure 机器学习数据准备 SDK 1.0.17

+ **新功能**
  + 现在支持使用表达式语言添加两个数值列来生成结果列。

+ **Bug 修复和改进**
  + 改进了 random_split 的文档和参数检查。
  
## <a name="2019-02-27"></a>2019-02-27

### <a name="azure-machine-learning-data-prep-sdk-v1016"></a>Azure 机器学习数据准备 SDK 1.0.16

+ **Bug 修复**
  + 修复了 API 更改导致的服务主体身份验证问题。

## <a name="2019-02-25"></a>2019-02-25

### <a name="azure-machine-learning-sdk-for-python-v1017"></a>用于 Python 的 Azure 机器学习 SDK 1.0.17

+ **新功能**

  + Azure 机器学习现在为常见的 DNN 框架 Chainer 提供一流的支持。 使用[`Chainer`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py)类用户可以轻松地训练和部署 Chainer 模型。
    + 了解如何[通过 ChainerMN 运行分布式培训](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/distributed-chainer/distributed-chainer.ipynb)
    + 了解如何[使用 HyperDrive 通过 Chainer 运行超参数优化](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb)
  + 添加 Azure 机器学习管道可根据数据存储修改触发管道运行。 管道[计划笔记本](https://aka.ms/pl-schedule)已更新，以展示此功能。

+ **Bug 修复和改进**
  + 我们添加了对 source_directory_data_store 属性的 Azure 机器学习支持，以便在[RunConfigurations](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py)上将属性设置为所需的数据存储（例如 blob 存储），并将其提供给[PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py)。 默认情况下，步骤使用 Azure 文件存储作为后备数据存储，如果同时执行大量步骤，则可能会遇到限制问题。

### <a name="azure-portal"></a>Azure 门户

+ **新功能**
  + 报表的新拖放表编辑器体验。 用户可以将列从表中拖到将显示表预览的表区域。 可以重新排列这些列。
  + 新建日志文件查看器
  + 从 "活动" 选项卡中试验运行、计算、模型、图像和部署的链接

### <a name="azure-machine-learning-data-prep-sdk-v1015"></a>Azure 机器学习数据准备 SDK 1.0.15

+ **新功能**
  + 数据准备现在支持从数据流写入文件流。 还提供操作文件流名称以创建新文件名的功能。
    + 操作方法指南：[使用文件流笔记本](https://aka.ms/aml-data-prep-file-stream-nb)

+ **Bug 修复和改进**
  + 提高了针对大型数据集的 t 摘要式性能。
  + 数据准备现在支持从数据路径读取数据。
  + 一个热编码现在适用于布尔值和数字列。
  + 其他 bug 修复。

## <a name="2019-02-11"></a>2019-02-11

### <a name="azure-machine-learning-sdk-for-python-v1015"></a>适用于 Python 的 Azure 机器学习 SDK v1.0.15

+ **新功能**
  + 添加了 AzureBatchStep （[笔记本](https://aka.ms/pl-azbatch)）、HyperDriveStep （笔记本）和基于时间的计划功能（[笔记本](https://aka.ms/pl-schedule)） Azure 机器学习管道。
  +  DataTranferStep 已更新，可以与 Azure SQL Server 和 Azure database for PostgreSQL ([notebook](https://aka.ms/pl-data-trans)) 配合使用。

+ **更改**
  + 弃用了 `PublishedPipeline.get_published_pipeline` 而改用 `PublishedPipeline.get`。
  + 弃用了 `Schedule.get_schedule` 而改用 `Schedule.get`。

### <a name="azure-machine-learning-data-prep-sdk-v1012"></a>Azure 机器学习数据准备 SDK v1.0.12

+ **新功能**
  + 数据准备现在支持使用数据存储从 Azure SQL 数据库读取数据。
 
+ **更改**
  + 提高了某些操作对大型数据的内存性能。
  + `read_pandas_dataframe()` 现在要求指定 `temp_folder`。
  + `ColumnProfile` 的 `name` 属性已弃用 - 请改用 `column_name`。

## <a name="2019-01-28"></a>2019-01-28

### <a name="azure-machine-learning-sdk-for-python-v1010"></a>适用于 Python 的 Azure 机器学习 SDK v1.0.10

+ **更改**： 
  + Azure 机器学习 SDK 不再将 azure-cli 包作为依赖项。 具体而言，azure-cli-core 和 azure-cli-profile 已从 azureml-core 中删除。 以下是对用户有所影响的更改：
    + 如果要执行 "az login"，然后使用 azureml-sdk，SDK 将多次执行浏览器或设备代码日志。 它不会使用“az login”所创建的任何凭据状态。
    + 对于 Azure CLI 身份验证（如使用“az login”），请使用 azureml.core.authentication.AzureCliAuthentication 类。 对于 Azure CLI 身份验证，请在安装了 azureml-sdk 的 Python 环境中执行 pip install azure-cli。
    + 如果在使用服务主体执行“az login”以实现自动化，则建议使用 azureml.core.authentication.ServicePrincipalAuthentication 类，因为 azureml-sdk 不会使用 azure CLI 所创建的凭据状态。 

+ **Bug 修复**：此版本主要包含次要 Bug 修复

### <a name="azure-machine-learning-data-prep-sdk-v108"></a>Azure 机器学习数据准备 SDK v1.0.8

+ **Bug 修复**
  + 提高了获取数据配置文件的性能。
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
+ 用于模型定型和批处理推理/计分
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
+ 新的[自动化机器学习图表](how-to-understand-automated-ml.md)适用于分类模型（带模型解释功能的提升、增益、校准功能重要性图表）和回归模型（带模型解释功能的残差和功能重要性图表）。 
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
