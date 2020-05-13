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
ms.openlocfilehash: 4372c7da2da67446bced99dd6650313cc6f1b3c5
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83123864"
---
# <a name="azure-machine-learning-release-notes"></a>Azure 机器学习发行说明

本文介绍 Azure 机器学习的版本。  有关完整的 SDK 参考内容，请访问 Azure 机器学习的[**适用于 Python 的主要 SDK**](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 参考页。

请参阅[已知问题列表](resource-known-issues.md)了解已知 bug 和解决方法。

## <a name="2020-05-11"></a>2020-05-11

### <a name="azure-machine-learning-sdk-for-python-v150"></a>用于 Python 的 Azure 机器学习 SDK

+ **新功能**
  + **预览功能**
    + **contrib-reinforcementlearning**
        + Azure 机器学习正在使用[Ray](https://ray.io) framework 发布预览版支持，强化学习。 `ReinforcementLearningEstimator`支持在 Azure 机器学习中跨 GPU 和 CPU 计算目标的强化 learning agent 培训。

+ **Bug 修复与改进**
  + **azure-cli-ml**
    + 修复以前的 PR 中意外遗留的警告日志。 日志用于调试，意外地遗留了。
    + Bug 修复：在分析过程中通知客户端部分失败
  + **azureml-automl-core**
    + 当数据集具有多个时间序列时，为时序启用并行管，加速 Prophet/AutoArima 模型在 automl 预测中的速度。 为了从此新功能中获益，建议你在 AutoMLConfig 中设置 "max_cores_per_iteration =-1" （即，使用所有可用的 cpu 内核）。
    + 修复 KeyError 在控制台界面中打印 guardrails
    + 修复了 experimentation_timeout_hours 的错误消息
    + AutoML 的不推荐使用的 Tensorflow 模型。
  + **azureml-automl-runtime**
    + 修复了 experimentation_timeout_hours 的错误消息
    + 修复了尝试从缓存存储进行反序列化时的未分类异常
    + 当数据集具有多个时间序列时，为时序启用并行管，加速 Prophet/AutoArima 模型在 automl 预测中的速度。
    + 修复了在测试/预测集不包含定型集粒度之一的数据集上，已启用滚动窗口的预测。
    + 改善丢失数据的处理
    + 修复了预测数据集期间预测间隔的问题，该数据集包含不在时间中对齐的时间序列。
    + 为预测任务添加了更好的数据形状验证。
    + 提高了频率检测。
    + 如果无法为预测任务生成交叉验证折叠，则创建更好的错误消息。
    + 修复控制台接口以正确打印缺失值 guardrail。
    + 在 AutoMLConfig 中对 cv_split_indices 输入强制执行数据类型检查。
  + **azureml-cli-common**
    + Bug 修复：在分析过程中通知客户端部分失败
  + **azureml-contrib-mir**
    + 添加一个 contrib. mir. RevisionStatus，其中中继有关当前已部署 MIR 修订版本和用户指定的最新版本的信息。 此类包含在 "deployment_status" 特性下的 MirWebservice 对象中。
    + 启用对 MirWebservice 类型及其子类 SingleModelMirWebservice 的 Webservices 的更新。
  + **contrib-reinforcementlearning**
    + 添加了 Ray 0.8.3 支持
    + AmlWindowsCompute 仅支持将 Azure 文件作为装入的存储
    + 将 health_check_timeout 重命名为 health_check_timeout_seconds
    + 修复了一些类/方法说明。
  + **azureml-core**
    + 已启用 WASB-在 USGovernment 和中国云中 > Blob 转换。
    + 修复 bug 以允许读者角色使用 az ml 运行 CLI 命令获取运行信息
    + 在 Azure ML 远程运行期间删除了输入数据集的不必要日志记录。
    + RCranPackage 现在支持 CRAN 包版本的 "version" 参数。
    + Bug 修复：在分析过程中通知客户端部分失败
    + 为 azureml 核心添加了欧洲形式的浮点处理。
    + Azure ml sdk 中启用的工作区私有链接功能。
    + 当使用创建 TabularDataset 时 `from_delimited_files` ，可以通过设置布尔参数来指定是否应将空值作为 None 或空字符串加载 `empty_as_string` 。
    + 添加了针对数据集的欧洲样式浮点处理。
    + 改进了数据集装载失败的错误消息。
  + **azureml-datadrift**
    + SDK 中的数据偏移结果查询有一个 bug，该 bug 未区分最小值、最大值和平均标准特征指标，导致值重复。 我们已修复此 bug，方法是将目标或基线作为度量值的名称。 之前：重复的最小值、最大值和平均值。 晚： target_min、target_max、target_mean、baseline_min、baseline_max、baseline_mean。
  + **azureml-dataprep**
    + 在确保数据传递所需的 .NET 依赖项时，提高写入受限 python 环境的处理能力。
    + 已在具有前导空记录的文件上修复了数据流创建。
    + 添加了类似于的错误处理选项 `to_partition_iterator` `to_pandas_dataframe` 。
  + **azureml-interpret**
    + 减小了解释路径长度限制，以减少超出 Windows 限制的可能性
    + 使用线性代理项模型通过模拟说明创建的稀疏说明的 bug 修复。
  + **azureml-opendatasets**
    + MNIST 列的修复问题被分析为字符串，该字符串应为 int。
  + **azureml-pipeline-core**
    + 使用嵌入 ModuleStep 中的模块时，允许选项 regenerate_outputs。
  + **azureml-train-automl-client**
    + AutoML 的不推荐使用的 Tensorflow 模型。
    + 在本地模式下修复用户允许列表不受支持的算法
    + AutoMLConfig 的文档修复。
    + 在 AutoMLConfig 中对 cv_split_indices 输入强制执行数据类型检查。
    + 修复了 show_output 中的 AutoML 运行失败的问题
  + **azureml-train-automl-runtime**
    + 修复系综迭代中的一个 bug，该 bug 阻止成功地从开始例行下载模型。
  + **azureml-train-core**
    + 修复 azureml. dnn. Nccl 类中的拼写错误。
    + 支持 PyTorch 估计器中的 PyTorch 版本1。5
    + 解决在使用定型 framework 估算时无法在 fairfax 区域中提取框架图像的问题

  
## <a name="2020-05-04"></a>2020-05-04
**全新笔记本体验**

你现在可以直接在 Azure 机器学习的 studio web 体验中创建、编辑和共享机器学习笔记本和文件。 你可以使用这些笔记本中[Azure 机器学习 PYTHON SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)中提供的所有类和方法，从[此处](https://docs.microsoft.com/azure/machine-learning/how-to-run-jupyter-notebooks)开始

**引入的新功能：**

+ VS Code 使用的增强型编辑器（摩纳哥编辑器） 
+ UI/UX 改进
+ 单元格工具栏
+ 新笔记本工具栏和计算控件
+ 笔记本状态栏 
+ 内联内核切换
+ R 支持
+ 辅助功能和本地化改进
+ 命令面板
+ 其他键盘快捷方式
+ 保存
+ 提高了性能和可靠性

从工作室访问以下基于 Web 的创作工具：
    
| 基于 Web 的工具  |     说明  | 版本 | 
|---|---|---|
| Azure ML Studio 笔记本   |     笔记本文件的第一类创作，支持 Azure ML Python SDK 中提供的所有操作。 | 基本和企业版  |   

## <a name="2020-04-27"></a>2020-04-27

### <a name="azure-machine-learning-sdk-for-python-v140"></a>用于 Python 的 Azure 机器学习 SDK 1.4。0

+ **新功能**
  + AmlCompute 群集现在支持在预配时在群集上设置托管标识。 只需指定是要使用系统分配的标识还是使用用户分配的标识，并在后传 identityId。 然后，你可以将 permissiosn 设置为访问各种资源（如存储或 ACR），方法是使用计算的标识来安全地访问数据，而不是 AmlCompute 目前使用的基于令牌的方法。 有关参数的详细信息，请参阅 SDK 参考。
  

+ **重大更改**
  + AmlCompute 群集支持基于运行的创建的预览功能，这是我们在两周内弃用的计划。 你可以继续使用 Amlcompute 类继续创建永久计算目标，但在不久的将来，将不支持指定标识符 "Amlcompute" 作为运行配置中的计算目标的特定方法。 

+ **Bug 修复与改进**
  + **azureml-automl-runtime**
    + 计算列中的唯一值的数目时，启用对 unhashable 类型的支持。
  + **azureml-core**
    + 使用 TabularDataset 从 Azure Blob 存储中进行读取时提高了稳定性。
    + 改进了的 `grant_workspace_msi` 参数的文档 `Datastore.register_azure_blob_store` 。
    + 修复了的 bug， `datastore.upload` 以支持以 `src_dir` 或结尾的参数 `/` `\` 。
    + 当尝试上传到没有访问密钥或 SAS 令牌的 Azure Blob 存储数据存储时，添加了可操作的错误消息。
  + **azureml-interpret**
    + 添加了上传说明的可视化数据的文件大小上限。
  + **azureml-train-automl-client**
    + 显式检查 AutoMLConfig 的 label_column_name & weight_column_name 参数是否为字符串类型。
  + **azureml-contrib-pipeline-steps**
    + ParallelRunStep 现在支持将 dataset 作为管道参数。 用户可以通过示例数据集构造管道，并且可以更改相同类型（文件或表格）的输入数据集，以进行新的管道运行。

  
## <a name="2020-04-13"></a>2020-04-13

### <a name="azure-machine-learning-sdk-for-python-v130"></a>用于 Python 的 Azure 机器学习 SDK 1.3。0

+ **Bug 修复与改进**
  + **azureml-automl-core**
    + 围绕定型后操作添加了其他遥测。
    + 通过使用长度超过100的序列的条件平方（CSS）定型，加速自动 ARIMA 培训。 请注意，使用的长度存储为 TimeSeriesInternal 类中/src/azureml-automl-core/azureml/automl/core/shared/constants.py 的常量 ARIMA_TRIGGER_CSS_TRAINING_LENGTH
    + 预测运行的用户日志记录已得到改进，现在日志中会显示有关当前正在运行的阶段的详细信息。
    + 不允许的 target_rolling_window_size 设置为小于2的值
  + **azureml-automl-runtime**
    + 改进了发现重复时间戳时显示的错误消息。
    + 不允许的 target_rolling_window_size 被设置为小于2的值。
    + 修复了 lag 插补法失败。 此问题是由随季节波动分解序列所需的观测数量不足引起的。 "Seasonalized" 数据用于计算分部 autocorrelation 函数（PACF）以确定 lag 长度。
    + 启用的列用途特征化通过特征化 config 预测任务的自定义。现在支持数值和分类为预测任务的列用途。
    + 已启用删除列特征化通过特征化 config 预测任务的自定义。
    + 已通过特征化 config 为预测任务启用插补法自定义。现在支持针对目标列和平均值、中间值、most_frequent 和常量值插补法的常量值插补法。
  + **azureml-contrib-pipeline-steps**
    + 接受要传递到 ParallelRunConfig 的字符串计算名称
  + **azureml-core**
    +  添加了环境克隆（new_name） API 以创建环境对象的副本
    +  Base_dockerfile 接受 filepath。 如果能够解析文件，则会将内容读入 base_dockerfile 环境属性
    + 当用户在环境中手动设置值时，自动为 base_image 和 base_dockerfile 重置互斥值
    + 在 RSection 中添加了 user_managed 标志，该标志指示环境是由用户管理还是由 AzureML 管理。
    + 数据集：如果数据路径包含 unicode 字符，则修复了数据集下载失败。
    + 数据集：改进的数据集装载缓存机制，以 Azure 机器学习计算中的最小磁盘空间要求，从而避免使节点不可用，并导致作业被取消。
    + 数据集：当你将 timeseries dataset 作为 pandas dataframes 进行访问时，我们将为 timeseries 列添加一个索引，用于加速对基于 timeseries 的数据访问的访问。  以前，索引的名称与时间戳列的名称相同，令用户感到困惑，这就是实际时间戳列和索引。 我们现在没有为索引指定任何特定名称，因为它不能用作列。 
    + 数据集：修复了主权云中的数据集身份验证问题。
    + 数据集：修复 `Dataset.to_spark_dataframe` 了从 Azure PostgreSQL 数据存储创建的数据集失败。
  + **azureml-interpret**
    + 如果本地重要性值稀疏，则将全局分数添加到可视化效果
    + 已更新的 azureml-解释为使用解释社区 0.9. *
    + 修复了下载具有稀疏计算数据的解释的问题
    + 添加了对 AutoML 中解释对象的稀疏格式的支持
  + **azureml-pipeline-core**
    + 支持在管道中将 ComputeInstance 作为计算目标
  + **azureml-train-automl-client**
    + 围绕定型后操作添加了其他遥测。
    + 修复了初期停止的回归
    + 弃用的 iris.dprep 作为输入数据的有效类型。
    +  将默认的 AutoML 试验超时更改为6天。
  + **azureml-train-automl-runtime**
    + 围绕定型后操作添加了其他遥测。
    + 添加了稀疏 automl e2e 支持
  + **azureml-opendatasets**
    + 为服务监视器添加了其他遥测。
    + 为 blob 启用 frontdoor 以提高稳定性 

## <a name="2020-03-23"></a>2020-03-23

### <a name="azure-machine-learning-sdk-for-python-v120"></a>用于 Python 的 Azure 机器学习 SDK 1.2。0

+ **重大更改**
  + 删除对 python 2.7 的支持

+ **Bug 修复与改进**
  + **azure-cli-ml**
    + 将 "--订阅 id" 添加到 `az ml model/computetarget/service` CLI 中的命令
    + 添加对传递客户托管密钥（CMK）的支持 vault_url、key_name 和 key_version 用于 ACI 部署
  + **azureml-automl-core** 
    + 为 X 和 y 数据预测任务启用了自定义插补法和常量值。
    + 修复了中的问题，并向用户显示错误消息。    
  + **azureml-automl-runtime**
    + 修复了对数据集进行预测的问题，其中包含只有一行的粒度
    + 减少了预测任务所需的内存量。
    + 如果时间列的格式不正确，则添加更好的错误消息。
    + 为 X 和 y 数据预测任务启用了自定义插补法和常量值。
  + **azureml-core**
    + 添加了对从环境变量加载 ServicePrincipal 的支持： AZUREML_SERVICE_PRINCIPAL_ID、AZUREML_SERVICE_PRINCIPAL_TENANT_ID 和 AZUREML_SERVICE_PRINCIPAL_PASSWORD
    + 引入了一个新 `support_multi_line` 参数 `Dataset.Tabular.from_delimited_files` ：默认情况下（ `support_multi_line=False` ），所有换行符（包括在带引号的字段值中）都将被解释为记录中断。 以这种方式读取数据比在多个 CPU 核心上并行执行更快且更具优化。 但是，它可能会导致以无提示方式生成更多的记录，字段值未对齐。 `True`当已知带引号的文件包含带引号的换行符时，此应设置为。
    + 添加了在 Azure 机器学习 CLI 中注册 ADLS Gen2 的功能
    + 对于 TabularDataset 中的 with_timestamp_columns （）方法，将参数 "fine_grain_timestamp" 重命名为 "timestamp"，将参数 "coarse_grain_timestamp" 重命名为 "partition_timestamp" 以更好地反映参数的用法。
    + 将最大实验名称长度增加到255。
  + **azureml-interpret**
    + 已更新的 azureml-解释为解释-社区 0.7. *
  + **azureml-sdk**
    + 更改为具有兼容的版本代字号的依赖项，支持在预发行和稳定版本中进行修补。


## <a name="2020-03-11"></a>2020-03-11

### <a name="azure-machine-learning-sdk-for-python-v115"></a>用于 Python 的 Azure 机器学习 SDK 1.1。5

+ **功能弃用**
  + **Python 2.7**
    + 支持 python 2.7 的最后版本

+ **重大更改**
  + **语义化版本控制 2.0.0**
    + 从版本1.1 开始，Azure ML Python SDK 采用语义版本控制2.0.0。 [在此处阅读详细信息](https://semver.org/)。 所有后续版本都将遵循新的编号方案和语义版本控制约定。 

+ **Bug 修复与改进**
  + **azure-cli-ml**
    + 将终结点 CLI 命令名称从 "az ml endpoint aks" 更改为 "az ml endpoint 实时" 以实现一致性。
    + 为稳定的试验性分支 CLI 更新 CLI 安装说明
    + 单实例分析已修复，可产生建议并在核心 sdk 中提供。
  + **azureml-automl-core**
    + 为 automl ONNX 模型启用了批处理模式推理（一次只接受多行）
    + 提高了对数据集的频率的检测，缺少数据或包含不规则的数据点
    + 添加了删除不符合主导频率的数据点的功能。
    + 更改了构造函数的输入，以获取用于为相应列应用插补法选项的选项的列表。
    + 错误日志记录已改进。
  + **azureml-automl-runtime**
    + 修复了在测试集中出现定型集不存在的颗粒时引发的错误的问题
    + 删除预测服务评分期间的 y_query 要求
    + 解决了当数据集包含带有长时间间隙的短粒度时的预测问题。
    + 解决了自动最大水平范围启用并且日期列包含字符串格式的日期时的问题。 当不能转换为 date 时添加了正确的转换和错误消息
    + 使用 native NumPy 和 SciPy 对 FileCacheStore 的中间数据进行序列化和反序列化（用于本地 AutoML）
    + 修复了失败的子运行可能会停滞在运行状态的错误。
    + 提高了特征化的速度。
    + 修复了评分期间的频率检查，此时预测任务不要求定型和测试集之间具有严格的频率等效性。
    + 更改了构造函数的输入，以获取用于为相应列应用插补法选项的选项的列表。
    + 修复了与 lag 类型选择相关的错误。
    + 修复了数据集上引发的未分类错误，使粒度具有单个行
    + 修复了 frequency 检测缓慢的问题。
    + 修复了 AutoML 异常处理中的 bug，该 bug 导致定型失败的实际原因被 AttributeError 取代。
  + **azureml-cli-common**
    + 单实例分析已修复，可产生建议并在核心 sdk 中提供。
  + **azureml-contrib-mir**
    + 添加 MirWebservice 类中的功能以检索访问令牌
    + 如果调用失败，则在 MirWebservice （）调用中默认使用令牌身份验证（& e）
    + Mir webservice 部署现在需要相应的 Sku [Standard_DS2_v2、Standard_F16 Standard_A2_v2]，而不是分别需要 [Ds2v2、A2v2 和 F16]。
  + **azureml-contrib-pipeline-steps**
    + 可选参数 side_inputs 添加到 ParallelRunStep。 此参数可用于将文件夹装载到容器上。 当前支持的类型为 DataReference 和 PipelineData。
    + 可以通过立即传递管道参数覆盖 ParallelRunConfig 中传递的参数。 支持的新管道参数 aml_mini_batch_size、aml_error_threshold、aml_logging_level、aml_run_invocation_timeout （aml_node_count 和 aml_process_count_per_node 已是早期版本的一部分）。
  + **azureml-core**
    + 部署的 AzureML Webservices 现在将默认为 `INFO` 日志记录。 这可以通过 `AZUREML_LOG_LEVEL` 在已部署的服务中设置环境变量来控制。
    + Python sdk 使用发现服务来使用 "api" 终结点，而不使用 "管道"。
    + 在所有 SDK 调用中交换到新路由。
    + 已将对 ModelManagementService 的调用的路由更改为新的统一结构。
      + 公开提供工作区更新方法。
      + 已在工作区更新方法中添加 image_build_compute 参数，以允许用户更新映像生成的计算。
    + 将弃用消息添加到旧的分析工作流。 固定的 cpu 和内存限制。
    + 在环境中添加了 RSection 以运行 R 作业。
    + 向添加了验证， `Dataset.mount` 以便在数据集的源不可访问或不包含任何数据时引发错误。
    + 添加 `--grant-workspace-msi-access` 为用于注册 Azure Blob 容器的数据存储 CLI 的附加参数，该参数可用于注册 VNet 后面的 Blob 容器。
    + 单实例分析已修复，可产生建议并在核心 sdk 中提供。
    + 修复了 aks.py _deploy 中的问题。
    + 验证要上传的模型的完整性以避免无提示存储失败。
    + 用户现在可以在为 webservices 重新生成密钥时为身份验证密钥指定一个值。
    + 修复了不能将大写字母用作数据集输入名称的 bug。
  + **azureml-默认值**
    + `azureml-dataprep`现在将作为的一部分进行安装 `azureml-defaults` 。 不再需要在计算目标上手动安装 dataprep [保险丝] 来装入数据集。
  + **azureml-interpret**
    + 已更新的 azureml-解释为解释-社区 0.6. *
    + 已更新的 azureml-解释为依赖于解读-社区0.5。0
    + 向 azureml-解读添加了 azureml 样式的例外
    + 修复了 keras 模型的 DeepScoringExplainer 序列化
  + **azureml-mlflow**
    + 向 azureml 添加对主权云的支持。 mlflow
  + **azureml-pipeline-core**
    + 管道批处理计分笔记本现在使用 ParallelRunStep
    + 修复了一个 bug，无论是否更改参数列表，PythonScriptStep 结果都可能会错误地重用
    + 添加了在上调用 parse_ * 方法时设置列的类型的功能`PipelineOutputFileDataset`
  + **azureml-pipeline-steps**
    + 将移 `AutoMLStep` 到 `azureml-pipeline-steps` 包中。 中已 `AutoMLStep` 弃用 `azureml-train-automl-runtime` 。
    + 添加了数据集的文档示例作为 PythonScriptStep 输入
  + **azureml-tensorboard**
    + 更新的 tensorboard 支持 tensorflow 2。0
    + 在计算实例上使用自定义 Tensorboard 端口时显示正确的端口号
  + **azureml-train-automl-client**
    + 解决了某些包可能安装在远程运行的不正确版本的问题。
    + 修复了用于筛选自定义特征化配置的问题的 FeaturizationConfig。
  + **azureml-train-automl-runtime**
    + 解决了远程运行中的频率检测问题
    + 已移动到 `AutoMLStep` `azureml-pipeline-steps` 包中。 中已 `AutoMLStep` 弃用 `azureml-train-automl-runtime` 。
  + **azureml-train-core**
    + 支持 PyTorch 估计器中的 PyTorch 版本1。4
  
## <a name="2020-03-02"></a>2020-03-02

### <a name="azure-machine-learning-sdk-for-python-v112rc0-pre-release"></a>用于 Python 1.1.2 rc0 的 Azure 机器学习 SDK （预发行版）

+ **Bug 修复与改进**
  + **azureml-automl-core**
    + 为 automl ONNX 模型启用了批处理模式推理（一次只接受多行）
    + 提高了对数据集的频率的检测，缺少数据或包含不规则的数据点
    + 添加了删除不符合主导 frequrncy 的数据点的功能。
  + **azureml-automl-runtime**
    + 修复了在测试集中出现定型集不存在的颗粒时引发的错误的问题
    + 删除预测服务评分期间的 y_query 要求
  + **azureml-contrib-mir**
    + 添加 MirWebservice 类中的功能以检索访问令牌
  + **azureml-core**
    + 部署的 AzureML Webservices 现在将默认为 `INFO` 日志记录。 这可以通过 `AZUREML_LOG_LEVEL` 在已部署的服务中设置环境变量来控制。
    + 修复了循环访问 `Dataset.get_all` 以返回注册到工作区的所有数据集。
    + 当向 `path` 数据集创建 api 的参数传递无效类型时，请改进错误消息。
    + Python sdk 使用发现服务来使用 "api" 终结点，而不使用 "管道"。
    + 交换到所有 SDK 调用中的新路由
    + 将对 ModelManagementService 的调用路由到新的统一结构
      + 公开提供工作区更新方法。
      + 已在工作区更新方法中添加 image_build_compute 参数，以允许用户更新映像生成的计算
    +  将弃用消息添加到旧的分析工作流。 固定分析 cpu 和内存限制
  + **azureml-interpret**
    + 更新 azureml-解释为解读-社区 0.6. *
  + **azureml-mlflow**
    + 向 azureml 添加对主权云的支持。 mlflow
  + **azureml-pipeline-steps**
    + 将移 `AutoMLStep` 到 `azureml-pipeline-steps package` 。 中已 `AutoMLStep` 弃用 `azureml-train-automl-runtime` 。
  + **azureml-train-automl-client**
    + 解决了某些包可能安装在远程运行的不正确版本的问题。
  + **azureml-train-automl-runtime**
    + 解决了远程运行中的频率检测问题
    + 将移 `AutoMLStep` 到 `azureml-pipeline-steps package` 。 中已 `AutoMLStep` 弃用 `azureml-train-automl-runtime` 。
  + **azureml-train-core**
    + 将移 `AutoMLStep` 到 `azureml-pipeline-steps package` 。 中已 `AutoMLStep` 弃用 `azureml-train-automl-runtime` 。

## <a name="2020-02-18"></a>2020-02-18

### <a name="azure-machine-learning-sdk-for-python-v111rc0-pre-release"></a>用于 Python 的 Azure 机器学习 SDK 版本 1.1.1 rc0 （预发行版）

+ **Bug 修复与改进**
  + **azure-cli-ml**
    + 单实例分析已修复，可产生建议并在核心 sdk 中提供。
  + **azureml-automl-core**
    + 错误日志记录已改进。
  + **azureml-automl-runtime**
    + 解决了当数据集包含带有长时间间隙的短粒度时的预测问题。
    + 解决了自动最大水平范围启用并且日期列包含字符串格式的日期时的问题。 如果无法转换为 date，我们添加了正确的转换和合理错误
    + 使用 native NumPy 和 SciPy 对 FileCacheStore 的中间数据进行序列化和反序列化（用于本地 AutoML）
    + 修复了失败的子运行可能会停滞在运行状态的错误。
  + **azureml-cli-common**
    + 单实例分析已修复，可产生建议并在核心 sdk 中提供。
  + **azureml-core**
    + 添加 `--grant-workspace-msi-access` 为用于注册 Azure Blob 容器的数据存储 CLI 的附加参数，该参数可用于注册 VNet 后面的 Blob 容器
    + 单实例分析已修复，可产生建议并在核心 sdk 中提供。
    + 修复了 aks.py _deploy 中的问题
    + 验证要上传的模型的完整性以避免无提示存储失败。
  + **azureml-interpret**
    + 向 azureml-解读添加了 azureml 样式的例外
    + 修复了 keras 模型的 DeepScoringExplainer 序列化
  + **azureml-pipeline-core**
    + 管道批处理计分笔记本现在使用 ParallelRunStep
  + **azureml-pipeline-steps**
    + 已移动到 `AutoMLStep` `azureml-pipeline-steps` 包中。 中已 `AutoMLStep` 弃用 `azureml-train-automl-runtime` 。
  + **azureml-contrib-pipeline-steps**
    + 可选参数 side_inputs 添加到 ParallelRunStep。 此参数可用于将文件夹装载到容器上。 当前支持的类型为 DataReference 和 PipelineData。
  + **azureml-tensorboard**
    + 更新的 tensorboard 支持 tensorflow 2。0
  + **azureml-train-automl-client**
    + 修复了用于筛选自定义特征化配置的问题的 FeaturizationConfig。
  + **azureml-train-automl-runtime**
    + 已移动到 `AutoMLStep` `azureml-pipeline-steps` 包中。 中已 `AutoMLStep` 弃用 `azureml-train-automl-runtime` 。
  + **azureml-train-core**
    + 支持 PyTorch 估计器中的 PyTorch 版本1。4
  
## <a name="2020-02-04"></a>2020-02-04

### <a name="azure-machine-learning-sdk-for-python-v110rc0-pre-release"></a>用于 Python 1.1.0 rc0 的 Azure 机器学习 SDK （预发行版）

+ **重大更改**
  + **语义化版本控制 2.0.0**
    + 从版本1.1 开始，Azure ML Python SDK 采用语义版本控制2.0.0。 [在此处阅读详细信息](https://semver.org/)。 所有后续版本都将遵循新的编号方案和语义版本控制约定。 
  
+ **Bug 修复与改进**
  + **azureml-automl-runtime**
    + 提高了特征化的速度。
    + 修复了评分期间的频率检查。现在，在预测任务中，我们不需要在定型集和测试集之间实现严格的频率等效。
  + **azureml-core**
    + 用户现在可以在为 webservices 重新生成密钥时为身份验证密钥指定一个值。
  + **azureml-interpret**
    + 已更新的 azureml-解释为依赖于解读-社区0.5。0
  + **azureml-pipeline-core**
    + 修复了一个 bug，无论是否更改参数列表，PythonScriptStep 结果都可能会错误地重用
  + **azureml-pipeline-steps**
    + 添加了数据集的文档示例作为 PythonScriptStep 输入
  + **azureml-contrib-pipeline-steps**
    + 可以通过立即传递管道参数覆盖 ParallelRunConfig 中传递的参数。 支持的新管道参数 aml_mini_batch_size、aml_error_threshold、aml_logging_level、aml_run_invocation_timeout （aml_node_count 和 aml_process_count_per_node 已是早期版本的一部分）。
  
## <a name="2020-01-21"></a>2020-01-21

### <a name="azure-machine-learning-sdk-for-python-v1085"></a>用于 Python 的 Azure 机器学习 SDK 1.0.85

+ **新功能**
  + **azureml-core**
    + 获取给定工作区和订阅中 AmlCompute 资源的当前核心使用情况和配额限制
  
  + **azureml-contrib-pipeline-steps**
    + 使用户能够将表格数据集作为上一步中的中间结果传递给 parallelrunstep

+ **Bug 修复与改进**
  + **azureml-automl-runtime**
    + 消除了对已部署预测服务的请求中 y_query 列的要求。 
    + 已从 Dominick 的橙色汁笔记本服务请求部分中删除 "y_query"。
    + 修复了对具有日期时间列的数据集进行操作，从而阻止预测已部署的模型。
    + 添加了 Matthews 相关性系数作为分类指标，适用于 binary 和多类分类。
  + **azureml-contrib-interpret**
    + 从 explainers 中删除了文本 "contrib"-解释为文本解释已移动到即将发布的解释文本存储库。
  + **azureml-core**
    + 数据集：文件数据集的使用情况不再依赖于要安装在 python 环境中的 numpy 和 pandas。
    + 更改了 wait_for_deployment LocalWebservice （）来检查本地 Docker 容器的状态，然后再尝试 ping 其运行状况终结点，从而大大减少了报告失败部署所需的时间量。
    + 修复了使用 LocalWebservice （）构造函数从现有部署创建服务对象时，在 LocalWebservice （）中使用的内部属性的初始化。
    + 已编辑错误消息以进行说明。
    + 向 AksWebservice 中添加了一个名为 get_access_token （）的新方法，该方法将返回 AksServiceAccessToken 对象，其中包含访问令牌、在时间戳后刷新、时间戳和标记类型的到期时间。 
    + AksWebservice 中不推荐使用的现有 get_token （）方法，因为新方法返回此方法返回的所有信息。
    + 已修改 az ml 服务 get 访问令牌命令的输出。 将令牌重命名为 accessToken，并将 refreshBy 重命名为 refreshAfter。 添加了 expiryOn 和 tokenType 属性。
    + 固定 get_active_runs
  + **azureml-说明模型**
    + 已将 shap 更新为0.33.0，并将社区解释为 0.4. *
  + **azureml-interpret**
    + 已将 shap 更新为0.33.0，并将社区解释为 0.4. *
  + **azureml-train-automl-runtime**
    + 添加了 Matthews 相关性系数作为分类指标，适用于 binary 和多类分类。
    + 弃用在代码中预处理标志并替换为特征化-特征化默认为启用

## <a name="2020-01-06"></a>2020-01-06

### <a name="azure-machine-learning-sdk-for-python-v1083"></a>用于 Python 的 Azure 机器学习 SDK 1.0.83

+ **新功能**
  + 数据集：添加两个选项 `on_error` ，并 `out_of_range_datetime` 使 `to_pandas_dataframe` 在数据具有错误值时失败，而不是用填充它们 `None` 。
  + 工作区：添加了 `hbi_workspace` 包含敏感数据的工作区标志，该标志可启用进一步的加密并对工作区禁用高级诊断。 通过 `cmk_keyvault` `resource_cmk_uri` 在创建工作区时指定和参数 Cosmos DB，我们还添加了对为关联的 Cosmos DB 实例引入自己的密钥的支持。 [在此处阅读详细信息。](https://docs.microsoft.com/azure/machine-learning/concept-enterprise-security#azure-cosmos-db)

+ **Bug 修复与改进**
  + **azureml-automl-runtime**
    + 修复了在 Python 版本下面的3.5.4 上运行 AutoML 时导致 TypeError 的回归。
  + **azureml-core**
    + 修复了在 `datastore.upload_files` 无法使用未以开头的相对路径的 bug `./` 。
    + 为所有 Image 类 codepaths 添加了弃用消息
    + 修复了 Mooncake 区域的模型管理 URL 构造。
    + 修复了无法为 Azure Functions 打包使用 source_dir 的模型的问题。    
    + 向环境添加了一个选项[build_local （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)以将映像推送到 AzureML 工作区容器注册表中
    + 更新了 SDK，以以后兼容的方式在 azure synapse 上使用新的令牌库。
  + **azureml-interpret**
    + 修复了不能下载任何说明时未返回任何内容的 bug。 现在引发异常，在其他位置匹配行为。
  + **azureml-pipeline-steps**
    + `DatasetConsumptionConfig` `Estimator` `inputs` 当在中使用时，不允许将传递给的参数 `Estimator` `EstimatorStep` 。
  + **azureml-sdk**
    + 已将 AutoML 客户端添加到 azureml-sdk 包，以便在不安装完整 AutoML 包的情况下提交远程 AutoML 运行。
  + **azureml-train-automl-client**
    + 更正了 automl 运行的控制台输出的对齐方式
    + 修复了可能在远程 amlcompute 上安装的 pandas 的版本不正确的 bug。

## <a name="2019-12-23"></a>2019-12-23

### <a name="azure-machine-learning-sdk-for-python-v1081"></a>适用于 Python 的 Azure 机器学习 SDK v1.0.81

+ **Bug 修复与改进**
  + **azureml-contrib-interpret**
    + 将 shap 依赖项从 azureml-interpret 推迟到 interpret-community
  + **azureml-core**
    + 现在可将计算目标指定为相应部署配置对象的参数。 具体而言，这是要部署到的计算目标的名称，而不是 SDK 对象的名称。
    + 已将 CreatedBy 信息添加到 Model 和 Service 对象。 可以通过 <var>.created_by 访问
    + 修复了无法正确设置 Docker 容器 HTTP 端口的 ContainerImage.run()。
    + 使 `azureml-dataprep` 成为 `az ml dataset register` cli 命令的可选参数
    + 修复了一个 bug，该 bug `TabularDataset.to_pandas_dataframe` 会错误地回退到备用读取器并输出警告。
  + **azureml-说明模型**
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
    + 改进了预测函数接口：y_pred 参数是可选的。 - 改进了 docstrings。
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
    + 从 azureml-train-automl<1.0.76 升级到 azureml-train-automl>=1.0.76 可能导致安装不完整，从而导致某些 automl 导入失败。 若要解决此问题，可以运行 https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/automl_setup.cmd 中的安装脚本。 如果直接使用 pip，则可以运行：
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
    + 调用导致删除 timeseries、label 或 image 列的 `keep_columns` 或 `drop_columns` 时，也会删除数据集的相应功能。
    + 修复了对象检测任务的 pytorch 加载程序存在的问题。
  + **azureml-contrib-interpret**
    + 从 azureml-contrib-interpret 中删除了解释仪表板小组件，并已将包更改为引用 interpret_community 中新的小组件
    + 已将 interpret-community 的版本更新为 0.2.0
  + **azureml-core**
    + 改进了 `workspace.datasets` 的性能。
    + 添加了使用用户名和密码身份验证注册 Azure SQL 数据库数据存储的功能
    + 修复了从相对路径加载 RunConfigurations 的问题。
    + 调用导致删除 timeseries 列的 `keep_columns` 或 `drop_columns` 时，也会删除数据集的相应功能。
  + **azureml-interpret**
    + 已将 interpret-community 的版本更新为 0.2.0
  + **azureml-pipeline-steps**
    + 阐述了 Azure 机器学习管道步骤支持的 `runconfig_pipeline_params` 值。
  + **azureml-pipeline-core**
    + 为 Pipeline 命令添加了用于下载 JSON 格式的输出的 CLI 选项。
  + **azureml-train-automl**
    + 已将 AzureML-Train-AutoML 拆分为 2 个包：一个客户端包 AzureML-Train-AutoML-Client，一个机器学习训练包 AzureML-Train-AutoML-Runtime
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
    + 修复了日志记录期间将某些用户信息掩码的问题。 - 改进了预测运行期间记录错误的方法。
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

的协作工作区登陆页面已 [https://ml.azure.com](https://ml.azure.com) 增强，并且更名为 Azure 机器学习 studio （预览版）。

在工作室中，可以训练、测试、部署和管理数据集、管道、模型、终结点等 Azure 机器学习资产。

从工作室访问以下基于 Web 的创作工具：

| 基于 Web 的工具 | 说明 | 版本 |
|-|-|-|
| 笔记本 VM（预览版） | 完全托管的基于云的工作站 | 基本和企业版 |
| [自动化机器学习](tutorial-first-experiment-automated-ml.md)（预览版） | 没有可用于自动化机器学习模型开发的代码体验 | Enterprise |
| [设计器](concept-designer.md)（预览版） | 前称为“设计器”的拖放式机器学习建模工具 | Enterprise |


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
  + 通过 [**azureml-datadrift**](https://docs.microsoft.com/python/api/azureml-datadrift) 包添加了数据集监视器，用于持续监视存在数据偏移的时序数据集或其他统计更改。 如果检测到偏移或符合针对数据的其他条件，则可以触发警报和事件。 有关详细信息，请参阅[我们的文档](https://aka.ms/datadrift)。
  + 在 Azure 机器学习中宣布两个新版本（也称为 SKU）。 在此版本中，现在可以创建“基本”或“企业”Azure 机器学习工作区。 所有现有工作区默认为“基本”版本，随时可以转到 Azure 门户或工作室来升级工作区。 可以从 Azure 门户创建“基本”或“企业”工作区。 有关详细信息，请阅读[我们的文档](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace)。 在 SDK 中，可以使用工作区对象的“sku”属性确定工作区的版本。
  + 我们还增强了 Azure 机器学习计算 - 现在，可以在 Azure Monitor 中查看群集的指标（例如节点总数、正在运行的节点、总核心配额），并可以查看用于调试的诊断日志。 此外，还可以查看群集上当前正在运行或已排队的运行，以及群集中各个节点的 IP 等详细信息。 可以在门户中查看这些信息，或者使用 SDK 或 CLI 中的相应函数来查看。

  + **预览功能**
    + 我们正在发布 Azure 机器学习计算中本地 SSD 磁盘加密的预览版支持。 若要将你的订阅加入允许列表以便能够使用此功能，请提交技术支持票证。
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
    + 添加了对复杂频率（例如 2 年和 1 个月）的支持。 - 添加了无法确定频率时显示的易于理解的错误消息。
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
    + [`Dataset.get_by_id`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29#get-by-id-workspace--id-)如果已注册数据集，则将更改为返回注册名称和版本。
    + 修复了无法反复使用将数据集用作参数的 ScriptRunConfig 来提交试验运行的 bug。
    + 将跟踪运行期间检索到的数据集，并可在运行详细信息页中查看，或者在 [`run.get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29#get-details--) 运行完成后调用。
    + 允许将 Azure 机器学习管道中的中间数据转换为表格数据集，并在中使用 [`AutoMLStep`](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlstep) 。
    + 添加了对部署和打包受支持模型（ONNX、scikit-learn 和 TensorFlow）的支持，无需 InferenceConfig 实例。
    + 在 SDK 和 CLI 中为服务部署（ACI 和 AKS）添加了覆盖标志。 如果提供此标志，将覆盖现有服务（如果存在同名的服务）。 如果服务不存在，将创建新服务。
    +  可将模型注册到 Onnx 和 Tensorflow 这两个新框架。 模型注册接受模型的示例输入数据、示例输出数据和资源配置。
    + 为 Azure Database for MySQL 添加了新的数据存储。 添加了有关在 Azure 机器学习管道的 DataTransferStep 中使用 Azure Database for MySQL 的示例。
    + 添加了在试验中添加和删除标记的功能；添加了从运行中删除标记的功能
    + 在 SDK 和 CLI 中为服务部署（ACI 和 AKS）添加了覆盖标志。 如果提供此标志，将覆盖现有服务（如果存在同名的服务）。 如果服务不存在，将创建新服务。
  + [**azureml-datadrift**](https://docs.microsoft.com/python/api/azureml-datadrift)
    + 已从 `azureml-contrib-datadrift` 移入 `azureml-datadrift`
    + 添加了监视时序数据集的偏移和其他统计度量的支持
    + 新方法 `create_from_model()` 和 `create_from_dataset()` [`DataDriftDetector`](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector(class)) 类。 `create()` 方法即将弃用。
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
    + 在估计器中添加了 PyTorch 1.3 支持 [`PyTorch`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch)

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
  + **contrib-datadrift**
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
  + **azureml-说明模型**
    + 将 AzureML-explain-model 包重命名为 AzureML-interpret，暂时保留旧包，以便能够后向兼容
    + 修复了 `automl` bug：从 ExplanationClient 下载时，原始解释默认设置为分类任务而不是回归
    + 添加了直接使用 `MimicWrapper` 创建 `ScoringExplainer` 的支持
  + **azureml-pipeline-core**
    + 改进了创建大管道的性能
  + **azureml-train-core**
    + 在 TensorFlow 评估器中添加了 TensorFlow 2.0 支持
  + **azureml-train-automl**
    + 创建[试验](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment)对象将获取或创建用于运行历史记录跟踪的 Azure 机器学习工作区中的试验。 创建时会在 Experiment 对象中填充试验 ID 和存档时间。 示例：

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
  + **azureml-说明模型**
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
    + 添加了组织有序的环境。 这些环境中已预先配置用于常用机器学习任务的库，并且已预先生成并缓存为 Docker 映像，以加快执行速度。 默认情况下，它们显示在[工作区](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29)的环境列表中，前缀为 "AzureML"。

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

  + **contrib-datadrift**
    + 已将包从 azureml-contrib-datadrift 重定位到 azureml-datadrift；未来的版本中将删除 `contrib` 包

+ **Bug 修复与改进**
  + **azureml-automl-core**
    + 已将 FeaturizationConfig 引入到 AutoMLConfig 和 AutoMLBaseSettings
    + 引入了 FeaturizationConfig 到[AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)和 AutoMLBaseSettings
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
    + 添加了 API `Dataset.Tabular.from_json_lines_files` 用于从 JSON 行文件创建 TabularDataset。 若要在有关 TabularDataset 的 JSON 行文件中了解此表格数据，请访问文档 https://aka.ms/azureml-data。
    + 为 supported_vmsizes() 函数添加了更多的 VM 大小字段（OS 磁盘、GPU 数目）
    + 为 list_nodes() 函数添加了更多的字段，以显示运行、专用 IP 和公共 IP、端口等。
    + 在群集预配期间可以指定新字段，可将 --remotelogin_port_public_access 设置为 enabled 或 disabled，具体取决于在创建群集时是要将 SSH 端口保留为打开还是关闭状态。 如果不指定此字段，服务将会根据是否在 VNet 中部署群集，智能地打开或关闭该端口。
  + **azureml-说明模型**
  + **[azureml-core](https://docs.microsoft.com/python/api/azureml-core/azureml.core)**
    + 添加了通过模型对象在存储中检索要建模的 SAS URL 的功能。 示例：model.[get_sas_url()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model#get-sas-urls--)
    + 引入了 run.[get_details](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29#get-details--)['datasets'] 用于获取与提交的运行关联的数据集
    + 添加了 API `Dataset.Tabular`.[from_json_lines_files()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-json-lines-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) 用于从 JSON 行文件创建 TabularDataset。 若要在有关 TabularDataset 的 JSON 行文件中了解此表格数据，请访问文档 https://aka.ms/azureml-data。
    + 为 [supported_vmsizes()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#supported-vmsizes-workspace--location-none-) 函数添加了更多的 VM 大小字段（OS 磁盘、GPU 数目）
    + 为 [list_nodes()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#list-nodes--) 函数添加了更多的字段，以显示运行、专用 IP 和公共 IP、端口等。
    + 在群集[预配期间可以指定新字段，可将 ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--) `--remotelogin_port_public_access` 设置为 enabled 或 disabled，具体取决于在创建群集时是要将 SSH 端口保留为打开还是关闭状态。 如果不指定此字段，服务将会根据是否在 VNet 中部署群集，智能地打开或关闭该端口。
  + **azureml-说明模型**
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
    + 添加了一个[笔记本](https://aka.ms/pl-modulestep)用于描述 [Module](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.module(class))、[ModuleVersion](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.moduleversion) 和 [ModuleStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep)。
  + **[azureml-pipeline-steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps)**
    + 添加了[RScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.rscriptstep)以支持通过 AML 管道运行 R 脚本。
    + 修复了 [AzureBatchStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.azurebatchstep) 中的元数据参数分析问题，此问题会导致出现错误消息“未指定参数 SubscriptionId 的分配”。
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + 支持将 training_data、validation_data、label_column_name 和 weight_column_name 用作数据输入格式。
    + 为[explain_model （）](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#explain-model-fitted-model--x-train--x-test--best-run-none--features-none--y-train-none----kwargs-)和[retrieve_model_explanations （）](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#retrieve-model-explanation-child-run-)添加了弃用消息。


## <a name="2019-09-16"></a>2019-09-16

### <a name="azure-machine-learning-sdk-for-python-v1062"></a>适用于 Python 的 Azure 机器学习 SDK v1.0.62

+ **新功能**
  + 针对 TabularDataset 引入了 `timeseries` 特征。 使用此特性可以轻松地按时间戳对 TabularDataset 的数据进行筛选，例如，获取某个时间范围内的所有数据或最新的数据。 若要了解针对 TabularDataset 的此 `timeseries` 特征，请访问 https://aka.ms/azureml-data 上的文档或 https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb 上的示例笔记本。
  + 支持使用 TabularDataset 和 FileDataset 进行训练。 请访问 https://aka.ms/dataset-tutorial 上的示例笔记本。

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
  + **azureml-说明模型**
    + 修复了在 "打包" python 包未安装时输出到控制台的警告： "使用的 lightgbm 早于支持的版本，请升级到大于2.2.1 的版本"
    + 修复了下载模型解释，提供包含许多特征的全局解释的分片
    + 在输出解释中修复了缺少初始化示例的模拟解释
    + 修复了使用两种不同类型的模型在解释客户端中上传期间 set 属性时发生的可变错误
    + 已将一个 get_raw 参数添加到评分解释器 .explain()，以便一个评分解释器可以返回工程值和原始值。
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

在此版本中，支持以下浏览器： Chrome、Firefox、Safari 和 Microsoft Edge 预览。

**已知问题：**

1. 如果出现 "出现错误，请刷新浏览器" 部署正在进行时加载区块文件时出错。

1. 不能删除或重命名笔记本和文件中的文件。 在公共预览版中，可以在笔记本 VM 中使用 Jupyter UI 或终端执行更新文件操作。 由于它是一个装载的网络文件系统，在笔记本 VM 上做出的所有更改会立即反映在笔记本工作区中。

1. 通过 SSH 连接到笔记本 VM：
   1. 找到在设置 VM 期间创建的 SSH 密钥。 或者，在 "Azure 机器学习" 工作区中找到密钥 > "打开计算" 选项卡，> 在列表中找到 "笔记本 VM" > 打开它的 "属性"：从对话框复制密钥。
   1. 将这些公共和专用 SSH 密钥导入本地计算机。
   1. 使用这些密钥通过 SSH 连接到笔记本 VM。

## <a name="2019-09-03"></a>2019-09-03
### <a name="azure-machine-learning-sdk-for-python-v1060"></a>适用于 Python 的 Azure 机器学习 SDK v1.0.60

+ **新功能**
  + 引入了 FileDataset，它引用数据存储或公共 URL 中的单个或多个文件。 这些文件可以采用任何格式。 FileDataset 提供下载文件或将其装载到计算资源的功能。 若要了解 FileDataset，请访问 https://aka.ms/file-dataset。
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
    + 不再需要“gpu_support”；AML 现在会自动检测并使用 nvidia docker 扩展（如果可用）。 未来的版本会将其删除。
    + 添加了创建、更新和使用 PipelineDrafts 的支持。
    + 此版本改进了自动化机器学习本地运行的执行性能。
    + 用户可以按名称从运行历史记录中查询指标。
    + 使用自定义异常而不是预测任务中的泛型改进了日志记录。
  + **azureml-说明模型**
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
  + 使 `TabularDataset` 能够被 AutomatedML 使用。 有关 `TabularDataset` 的详细信息，请访问 https://aka.ms/azureml/howto/createdatasets。

+ **Bug 修复与改进**
  + **automl-client-core-nativeclient**
    + 修复了以 pandas 数据帧格式而不是以 numpy 数组形式提供训练和/或验证标签（y 和 y_valid）时引发的错误。
    + 更新了用于创建 `RawDataContext` 的接口，现在只需要数据和 `AutoMLBaseSettings` 对象。
    +  允许 AutoML 用户在预测时删除不够长的训练系列。 - 允许 AutoML 用户在预测时从测试集中删除不在训练集中的粒度。
  + **azure-cli-ml**
    + 你现在可以为在 AKS 群集上部署的用于 Microsoft 生成的评分终结点和客户证书更新 TLS/SSL 证书。
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
    + 你现在可以为在 AKS 群集上部署的用于 Microsoft 生成的评分终结点和客户证书更新 TLS/SSL 证书。
  + **azureml-说明模型**
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
  + 预览版中发布了在工作区级别设置配额的功能。 AmlCompute 配额将在订阅级别分配，但我们现在允许在工作区之间分发该配额，并分配该配额来实现公平共享和监管。 只需单击工作区左侧导航栏中的 "**使用情况 + 配额**" 边栏选项卡，然后选择 "**配置配额**" 选项卡。请注意，必须是订阅管理员才能在工作区级别设置配额，因为这是一个跨工作区操作。

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
    + 小组件更改：使用自动安装 `contrib` ，无更多 `nbextension` 安装/启用-支持说明，仅具有全局功能重要性（例如 Permutative）
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
    + 为新区域添加 appinsifht 映射-centralus-westus-northcentralus
    + 为所有 Datastore 类中的所有特性添加了文档。
    + 已将 blob_cache_timeout 参数添加到 `Datastore.register_azure_blob_container`。
    + 已将 save_to_directory 和 load_from_directory 方法添加到 azureml.core.environment.Environment。
    + 已将“az ml environment download”和“az ml environment register”命令添加到 CLI。
    + 添加了 Environment.add_private_pip_wheel 方法。
  + **azureml-说明模型**
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
  + **automl-client-core-nativeclient**
    + 修复了在转换后丢失列类型的 bug（链接的 bug）；
    + 允许 y_query 是开头包含 None 的对象类型 (#459519)。
  + **azure-cli-ml**
    + CLI 命令“model deploy”和“service update”现在接受参数、配置文件或二者的组合。 文件中的参数优先于特性。
    + 现在，在注册后可以更新模型说明
  + **azureml-automl-core**
    + 将 NimbusML 依赖项更新为版本 1.2.0（当前的最新版本）。
    + 即将添加在 AutoML 评估器中使用 NimbusML 评估器和管道的支持。
    + 即将修复系综选择过程中的一个 bug：即使评分保持恒定，此 bug 也会不必要地增大生成的系综。
    + 允许跨预测任务的 CV 拆分重复使用某些特征化。 对于高开销的特征化（例如滞后和滚动窗口），这可以大致按 n_cross_validations 的系数加快设置运行的运行时。
    + 即将解决当时间超出 pandas 支持的时间范围时发生的某个问题。 现在，如果时间小于 pd.Timestamp.min 或大于 pd.Timestamp.max，将引发 DataException
    + 如果训练集和测试集可以对齐，则预测现在允许在这些集中使用不同的频率。 例如，"从一月开始的季度" 和 "10 月开始的季度" 可以对齐。
    + 已将“parameters”属性添加到 TimeSeriesTransformer。
    + 删除了旧异常类。
    + 在预测任务中，`target_lags` 参数现在接受单个整数值，或整数列表。 如果提供整数，则只会创建一个滞后对象。 如果提供列表，则采用滞后对象的唯一值。 target_lags=[1, 2, 2, 4] 将创建 1 个、2 个和 4 个时段的滞后对象。
    + 修复了在转换后丢失列类型的 bug（链接的 bug）；
    + 在 `model.forecast(X, y_query)` 中，允许 y_query 是开头包含 None 的对象类型 (#459519)。
    + 已将预期值添加到 `automl` 输出
  + **contrib-datadrift**
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
    + Bug 修复：由于上游依赖关系，模型和图像删除现在提供了有关检索依赖于它们的上游对象的详细信息。
    + 修复了以下 bug：在为某些环境创建工作区时输出部署的空白持续时间。
    + 改进了工作区创建失败异常。 用户不再会看到“无法创建工作区。 找不到...”消息，而是看到实际的创建失败。
    + 在 AKS Web 服务中添加了令牌身份验证的支持。
    + 已将 `get_token()` 方法添加到 `Webservice` 对象。
    + 添加了管理机器学习数据集的 CLI 支持。
    + `Datastore.register_azure_blob_container` 现在可以选择性地采用 `blob_cache_timeout` 值（以秒为单位），该值配置 blobfuse 的 mount 参数，以便为此数据存储启用缓存过期。 默认值为无超时，即，在读取某个 Blob 时，该 Blob 将保留在本地缓存中，直到作业完成。 大多数作业优先使用此设置，但某些作业需要从大型数据集读取更多数据，而其节点无法容纳此数据集。 优化此参数有助于这些作业成功完成。 优化此参数时请小心：设置太小的值可能会导致性能不佳，因为在 epoch 中使用的数据在再次使用之前可能会过期。 这意味着，将从 Blob 存储（即网络）而不是本地缓存完成所有读取，这会对训练时间产生负面影响。
    + 现在，在注册后可以正常更新模型说明
    + 模型和映像删除操作现在会提供有关依赖于它们的上游对象（这些对象导致删除失败）的更多信息
    + 使用 azureml.mlflow 改进了远程运行的资源利用率。
  + **azureml-说明模型**
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
    + 修复了 AutoMLStep 中的 whitelist_models 和 blacklist_models 设置问题。
    + 修复了在 Azure ML 管道上下文中使用 AutoML 时阻止使用预处理的问题。
  + **azureml-opendatasets**
    + 已将 azureml-contrib-opendatasets 移到 azureml-opendatasets。
    + 允许将打开的数据集类注册到 Azure 机器学习工作区并无缝利用 AML 数据集功能。
    + 显著改进了非 SPARK 版本中的 NoaaIsdWeather 扩充性能。
  + **azureml-说明模型**
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
    + AzureBatchStep：有关输入/输出的改进文档。
    + AzureBatchStep： delete_batch_job_after_finish 默认值更改为 true。
  + **azureml-train-core**
    + 现在支持将字符串用作自动超参数优化的计算目标。
    + 已弃用 auto_prepare_environment 中未使用的 RunConfiguration 设置。
    + 已弃用参数 `conda_dependencies_file_path` 和 `pip_requirements_file_path`，分别以 `conda_dependencies_file` 和 `pip_requirements_file` 取代。
  + **azureml-opendatasets**
    + 显著改进了非 SPARK 版本中的 NoaaIsdWeather 扩充性能。

### <a name="azure-machine-learning-data-prep-sdk-v118"></a>Azure 机器学习数据准备 SDK v1.1.8

+ **新功能**
 + 现在可以迭代数据流对象，生成一系列记录。 请参阅 `Dataflow.to_record_iterator` 的文档。
  + 现在可以迭代数据流对象，生成一系列记录。 请参阅 `Dataflow.to_record_iterator` 的文档。

+ **Bug 修复与改进**
 + 提高了 DataPrep SDK 的稳健性。
 + 改进了包含非字符串列索引的 pandas 数据帧的处理。
 + 改进了数据集中的 `to_pandas_dataframe` 性能。
 + 修复了以下 bug：在多节点环境中运行时，数据集的 Spark 执行失败。
  + 提高了 DataPrep SDK 的稳健性。
  + 改进了包含非字符串列索引的 pandas 数据帧的处理。
  + 改进了数据集中的 `to_pandas_dataframe` 性能。
  + 修复了以下 bug：在多节点环境中运行时，数据集的 Spark 执行失败。

## <a name="2019-07-01"></a>2019-07-01

### <a name="azure-machine-learning-data-prep-sdk-v117"></a>Azure 机器学习数据准备 SDK v1.1.7

我们撤消了一项改进性能的更改，因为此项更改会导致某些客户在使用 Azure Databricks 时遇到问题。 如果遇到 Azure Databricks 的问题，可以使用以下方法之一升级到版本 1.1.7：
1. 运行以下脚本进行升级：`%sh /home/ubuntu/databricks/python/bin/pip install azureml-dataprep==1.1.7`
2. 重新创建群集，这会安装最新的数据准备 SDK 版本。

## <a name="2019-06-25"></a>2019-06-25

### <a name="azure-machine-learning-sdk-for-python-v1045"></a>适用于 Python 的 Azure 机器学习 SDK v1.0.45

+ **新功能**
  + 在 azureml-explain-model 包中为模拟解释器添加了决策树代理项模型
  + 可以指定要在推理映像中安装的 CUDA 版本。 支持 CUDA 9.0、9.1 和 10.0。
  + [Azure ML 容器 GitHub 存储库](https://github.com/Azure/AzureML-Containers)和 [DockerHub](https://hub.docker.com/_/microsoft-azureml) 中现已提供有关 Azure ML 训练基础映像的信息
  + 为管道计划添加了 CLI 支持。 运行“az ml pipeline -h”可了解详细信息
  + 已将自定义 Kubernetes 命名空间参数添加到 AKS Web 服务部署配置和 CLI。
  + 已弃用所有管道步骤的 hash_paths 参数
  + Model.register 现在支持使用 `child_paths` 参数将多个单独的文件注册为单个模型。

+ **预览功能**
    + 评分解释器现在可以选择性地保存 conda 和 pip 信息，使序列化和反序列化更可靠。
    + 自动特征选择器的 Bug 修复。
    + 已将 mlflow.azureml.build_image 更新到新的 API，并已修补新实现公开的 bug。

+ **Bug 修复与改进**
  + 从 azureml-core 中删除了 `paramiko` 依赖项。 为旧版计算目标附加方法添加了弃用警告。
  + 改进了 run.create_children 的性能
  + 在使用二元分类器的模拟解释器中，修复了对缩放造型值使用教学器概率时的概率顺序。
  + 改进了自动化机器学习的错误处理和消息。
  + 修复了自动化机器学习的迭代超时问题。
  + 改进了自动化机器学习的时序转换性能。

## <a name="2019-06-24"></a>2019-06-24

### <a name="azure-machine-learning-data-prep-sdk-v116"></a>Azure 机器学习数据准备 SDK v1.1.6

+ **新功能**
  + 为上限值 (`SummaryFunction.TOPVALUES`) 和下限值 (`SummaryFunction.BOTTOMVALUES`) 添加了汇总函数。

+ **Bug 修复与改进**
  + 显著改进了 `read_pandas_dataframe` 的性能。
  + 修复了导致指向二进制文件的数据流中的 `get_profile()` 失败的 bug。
  + 公开了 `set_diagnostics_collection()`，以允许以编程方式启用/禁用遥测收集。
  + 更改了 `get_profile()` 的行为。 现在将会忽略 Min、Mean、Std 和 Sum 的 NaN 值，这与 Pandas 的行为保持一致。


## <a name="2019-06-10"></a>2019-06-10

### <a name="azure-machine-learning-sdk-for-python-v1043"></a>适用于 Python 的 Azure 机器学习 SDK v1.0.43

+ **新功能**
  + Azure 机器学习现在为流行的机器学习和数据分析框架 Scikit-learn 提供一流支持。 使用[ `SKLearn` 估计器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py)，用户可以轻松地训练和部署 scikit-learn 模型。
    + 了解如何[使用 HyperDrive 通过 Scikit-learn 运行超参数优化](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb)。
  + 添加了在管道中创建 ModuleStep 的支持，并添加了用于管理可重用计算单位的 Module 和 ModuleVersion 类。
  + ACI Web 服务现在通过更新支持持久性 scoring_uri。 scoring_uri 将从 IP 更改为 FQDN。 可以通过在 deploy_configuration 中设置 dns_name_label，来配置 FQDN 的 DNS 名称标签。
  + 自动化机器学习的新功能：
    + 用于预测的 STL 特征化器
    + 为功能扫描启用 K 平均聚集
  + AmlCompute 配额审批速度更快！ 我们现已自动化在阈值范围内审批配额请求的过程。 有关配额工作原理的详细信息，请参阅[如何管理配额](https://docs.microsoft.com/azure/machine-learning/how-to-manage-quotas)。

+ **预览功能**
    + 通过 azureml-mlflow 包与 [MLflow](https://mlflow.org) 1.0.0 跟踪相集成（[示例笔记本](https://aka.ms/azureml-mlflow-examples)）。
    + 将 Jupyter 笔记本作为运行提交。 [API 参考文档](https://docs.microsoft.com/python/api/azureml-contrib-notebook/azureml.contrib.notebook?view=azure-ml-py)
    + 通过 azureml-contrib-datadrift 包提供[数据偏移检测器](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector(class))公共预览版（[示例笔记本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/monitor-models/data-drift)）。 数据偏移是模型准确度不断下降的主要原因之一。 当为生产环境中的模型提供的数据不同于训练模型所用的数据时，就会发生数据偏移。 AML 数据偏移检测器可帮助客户监视数据偏移，并在检测到偏移时发送警报。

+ **重大更改**

+ **Bug 修复与改进**
  + RunConfiguration 加载和保存操作支持为指定完整文件路径，以便与以前的行为完全后向兼容。
  + 在 ServicePrincipalAuthentication 中添加了缓存，但默认会禁用缓存。
  + 在同一指标名称下启用多个绘图的日志记录。
  + 现在可从 azureml.core 正常导入 Model 类 (`from azureml.core import Model`)。
  + 在管道步骤中，`hash_path` 参数现已弃用。 新行为是哈希处理整个 source_directory，但 .amlignore 或 .gitignore 中列出的文件除外。
  + 在管道包中，各个 `get_all` 和 `get_all_*` 方法已弃用，分别以 `list` 和 `list_*` 取代。
  + 在返回原始运行类型之前，azureml.core.get_run 不再需要导入类。
  + 修复了对 Web 服务更新的某些调用不触发更新的问题。
  + AKS Web 服务中的评分超时应介于 5 毫秒与 300000 毫秒之间。 评分请求的最大允许 scoring_timeout_ms 已从 1 分钟提高到 5 分钟。
  + LocalWebservice 对象现在具有 `scoring_uri` 和 `swagger_uri` 属性。
  + 已将输出目录创建和输出目录上传移出用户进程。 使运行历史记录 SDK 能够在每个用户进程中运行。 这应该可以解决分布式训练运行遇到的某些同步问题。
  + 从用户进程名称写入的 azureml 日志名称现在包含进程名称（仅适用于分布式训练）和 PID。

### <a name="azure-machine-learning-data-prep-sdk-v115"></a>Azure 机器学习数据准备 SDK v1.1.5

+ **Bug 修复与改进**
  + 对于采用两位数年份格式的解释日期时间值，有效年份的范围已更新，以便与 Windows 五月发行版相匹配。 范围已从 1930-2029 更改为 1950-2049。
  + 读入文件和设置 `handleQuotedLineBreaks=True` 时，`\r` 将被视为新行。
  + 修复了在某些情况下导致 `read_pandas_dataframe` 失败的 bug。
  + 改进了 `get_profile` 的性能。
  + 改进了错误消息。

## <a name="2019-05-28"></a>2019-05-28

### <a name="azure-machine-learning-data-prep-sdk-v114"></a>Azure 机器学习数据准备 SDK v1.1.4

+ **新功能**
  + 现在，可以使用以下表达式语言函数在新列中提取和分析日期时间值。
    + `RegEx.extract_record()` 将日期时间元素提取到新列中。
    + `create_datetime()` 从单独的日期时间元素创建日期时间对象。
  + 调用 `get_profile()` 时，现在可以看到分位数列标记为 (est.)，以明确指示值为近似值。
  + 从 Azure Blob 存储读取时，现在可以使用 ** 组合通配符。
    + 例如，`dprep.read_csv(path='https://yourblob.blob.core.windows.net/yourcontainer/**/data/*.csv')`

+ **Bug 修复**
  + 修复了从远程源 (Azure Blob) 读取 Parquet 文件相关的 bug。

## <a name="2019-05-14"></a>2019-05-14

### <a name="azure-machine-learning-sdk-for-python-v1039"></a>适用于 Python 的 Azure 机器学习 SDK v1.0.39
+ **更改**
  + 即将弃用运行配置 auto_prepare_environment 选项，auto prepare 将成为默认选项。

## <a name="2019-05-08"></a>2019-05-08

### <a name="azure-machine-learning-data-prep-sdk-v113"></a>Azure 机器学习数据准备 SDK v1.1.3

+ **新功能**
  + 添加了通过调用 read_postgresql 或使用数据存储从 PostgresSQL 数据库读取数据的支持。
    + 请参阅操作指南中的示例：
      + [数据引入笔记本](https://aka.ms/aml-data-prep-ingestion-nb)
      + [数据存储笔记本](https://aka.ms/aml-data-prep-datastore-nb)

+ **Bug 修复与改进**
  + 修复了列类型转换的问题：
  + 现在可将布尔值列或数字列正确转换为布尔值列。
  + 现在，在尝试将日期列设置为日期类型时不会失败。
  + 改进了 JoinType 类型和随附的参考文档。 联接两个数据流时，现在可以指定以下联接类型之一：
    + NONE、MATCH、INNER、UNMATCHLEFT、LEFTANTI、LEFTOUTER、UNMATCHRIGHT、RIGHTANTI、RIGHTOUTER、FULLANTI、FULL。
  + 改进了数据类型推理以识别更多日期格式。

## <a name="2019-05-06"></a>2019-05-06

### <a name="azure-portal"></a>Azure 门户

在 Azure 门户中，现在可以：
+ 创建和运行自动化 ML 试验
+ 创建笔记本 VM，以试用示例 Jupyter 笔记本或自己的笔记本。
+ Azure 机器学习工作区中包含全新的“创作”部分（预览版），其中包括自动化机器学习、可视化界面和托管的笔记本 VM
    + 使用自动化机器学习自动创建模型
    + 使用拖放式可视化界面运行试验
    + 创建笔记本 VM 来浏览数据、创建模型和部署服务。
+ 运行报告和运行详细信息页中的图表和指标会实时更新
+ 更新了运行详细信息页中日志、输出和快照的文件查看器。
+ “试验”选项卡中提供新的和改进的报告创建体验。
+ 添加了从 Azure 机器学习工作区的“概述”页下载 config.json 文件的功能。
+ 支持从 Azure Databricks 工作区创建 Azure 机器学习工作区。

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033"></a>适用于 Python 的 Azure 机器学习 SDK v1.0.33
+ **新功能**
  + _Workspace.create_ 方法现在接受 CPU 和 GPU 群集的默认群集配置。
  + 如果工作区创建失败，将清理依赖的资源。
  + 默认 Azure 容器注册表 SKU 已切换为“基本”。
  + Azure 容器注册表是在创建运行或映像时根据需要惰式创建的。
  + 支持用于训练运行的环境。

### <a name="notebook-virtual-machine"></a>笔记本虚拟机 

使用笔记本 VM 作为 Jupyter 笔记本的安全且企业就绪的托管环境，在其中可以编写机器学习试验、将模型部署为 Web 终结点，以及使用 Python 执行 Azure 机器学习 SDK 支持的所有其他操作。它提供多种功能：
+ [快速启动预配置的笔记本 VM](tutorial-1st-experiment-sdk-setup.md)  具有最新版本的 Azure 机器学习 SDK 和相关包。
+ 通过 HTTPS、Azure Active Directory 身份验证和授权等经过证实的技术来保护访问。
+ 为 Azure 机器学习工作区 Blob 存储帐户中的笔记本和代码提供可靠的云存储。可以安全地删除笔记本 VM，而不会丢失工作。
+ 使用预装的示例笔记本来探索和体验 Azure 机器学习功能。
+ Azure VM、任何 VM 类型、任何包和任何驱动程序的完整自定义功能。 

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

[`PipelineEndpoint`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline_endpoint.pipelineendpoint?view=azure-ml-py)介绍了如何在保持同一终结点的情况下添加新版本的已发布管道。

## <a name="2019-04-17"></a>2019-04-17

### <a name="azure-machine-learning-data-prep-sdk-v112"></a>Azure 机器学习数据准备 SDK v1.1.2

注意：数据准备 Python SDK 将不再安装 `numpy` 和 `pandas` 包。 请参阅[更新的安装说明](https://github.com/Microsoft/AMLDataPrepDocs)。

+ **新功能**
  + 现在可以使用 Pivot 转换。
    + 操作方法指南：[透视笔记本](https://aka.ms/aml-data-prep-pivot-nb)
  + 现在可以在本机函数中使用正则表达式。
    + 例如：
      + `dflow.filter(dprep.RegEx('pattern').is_match(dflow['column_name']))`
      + `dflow.assert_value('column_name', dprep.RegEx('pattern').is_match(dprep.value))`
  + 现在可以在表达式语言中使用 `to_upper` 和 `to_lower` 函数。
  + 现在，可以看到数据配置文件中每个列的唯一值数。
  + 对于某些常用的读取器步骤，现在可以传入 `infer_column_types` 参数。 如果设置为 `True`，数据准备将尝试检测并自动转换列类型。
    + `inference_arguments` 现已弃用。
  + 现在可以调用 `Dataflow.shape`。

+ **Bug 修复与改进**
  + `keep_columns` 现在接受附加的可选参数 `validate_column_exists`，该参数检查 `keep_columns` 的结果是否包含任何列。
  + 所有读取器步骤（从文件读取）现在接受附加的可选参数 `verify_exists`。
  + 改进了读取 pandas 数据帧和获取数据配置文件的性能。
  + 修复了以下 bug：将数据流中的单个步骤切片失败并返回单个索引。

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

### <a name="azure-machine-learning-data-prep-sdk-v111"></a>Azure 机器学习数据准备 SDK v1.1.1

+ **新功能**
  + 可以使用 read_* 转换读取多个 Datastore/DataPath/DataReference 源。
  + 可对列执行以下运算来创建新列：除法、向下取整、取模、乘方、求长。
  + 数据准备现在是 Azure ML 诊断套件的一部分，默认会记录诊断信息。
    + 若要关闭此功能，请将此环境变量设置为 true： DISABLE_DPREP_LOGGER

+ **Bug 修复与改进**
  + 改进了常用类和函数的代码文档。
  + 修复了 auto_read_file 中无法读取 Excel 文件的 bug。
  + 添加了用于在 read_pandas_dataframe 中覆盖文件夹的选项。
  + 改进了 dotnetcore2 依赖项安装的性能，并添加了对 Fedora 27/28 和 Ubuntu 1804 的支持。
  + 改进了从 Azure Blob 读取数据的性能。
  + 列类型检测现在支持 Long 类型的列。
  + 修复了以下 bug：某些日期值显示为时间戳而不是 Python 日期时间对象。
  + 修复了以下 bug：某些类型计数显示为双精度值而不是整数。


## <a name="2019-03-25"></a>2019-03-25

### <a name="azure-machine-learning-sdk-for-python-v1021"></a>适用于 Python 的 Azure 机器学习 SDK v1.0.21

+ **新功能**
  + 使用 *azureml.core.Run.create_children* 方法可以通过单次调用以较低的延迟创建多个子运行。

### <a name="azure-machine-learning-data-prep-sdk-v110"></a>Azure 机器学习数据准备 SDK v1.1.0

+ **重大更改**
  + 数据准备包的概念已弃用，且不再受支持。 可以单独保存数据流，而不是将多个数据流保存在一个包中。
    + 操作方法指南：[打开并保存数据流笔记本](https://aka.ms/aml-data-prep-open-save-dataflows-nb)

+ **新功能**
  + 数据准备现在可以识别与特定语义类型匹配的列，并相应地进行拆分。 当前支持的 STypes 包括：电子邮件地址、地理坐标（纬度和经度）、IPv4 和 IPv6 地址、美国电话号码和美国邮政编码。
    + 操作方法指南：[语义类型笔记本](https://aka.ms/aml-data-prep-semantic-types-nb)
  + 数据准备现在支持使用以下运算从两个数字列生成结果列：减法、乘法、除法和取模。
  + 可对数据流调用 `verify_has_data()`，以检查数据流是否会在执行时生成记录。

+ **Bug 修复与改进**
  + 现在可以指定要在数字列分析直方图中使用的箱数。
  + `read_pandas_dataframe` 转换现在要求数据帧具有字符串或字节类型的列名称。
  + 修复了 `fill_nulls` 转换中的 bug：如果缺少列，将不会正确填充值。

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

  + Azure 机器学习现在为流行的 DNN 框架 Chainer 提供一流的支持。 使用 [`Chainer`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) 类用户可以轻松地训练和部署 Chainer 模型。
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

### <a name="azure-machine-learning-data-prep-sdk-v1015"></a>Azure 机器学习数据准备 SDK v1.0.15

+ **新功能**
  + 数据准备现在支持从数据流写入文件流。 此外，提供操控文件流名称以创建新文件名的功能。
    + 操作方法指南：使用[文件流笔记本](https://aka.ms/aml-data-prep-file-stream-nb)

+ **Bug 修复与改进**
  + 改进了针对大型数据集的 t-Digest 性能。
  + 数据准备现在支持从数据路径读取数据。
  + 现在，一个热编码即可处理布尔值列和数字列。
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
  + 改进了对大型数据的某些操作的内存性能。
  + `read_pandas_dataframe()` 现在要求指定 `temp_folder`。
  + `ColumnProfile` 的 `name` 属性已弃用 - 请改用 `column_name`。

## <a name="2019-01-28"></a>2019-01-28

### <a name="azure-machine-learning-sdk-for-python-v1010"></a>适用于 Python 的 Azure 机器学习 SDK v1.0.10

+ **更改**：
  + Azure 机器学习 SDK 不再将 azure-cli 包作为依赖项。 具体而言，azure-cli-core 和 azure-cli-profile 已从 azureml-core 中删除。 以下是对用户有所影响的更改：
      + 如果在执行“az login”，然后使用 azureml-sdk，则 SDK 会再次执行浏览器或设备代码登录。 它不会使用“az login”所创建的任何凭据状态。
    + 对于 Azure CLI 身份验证（如使用“az login”），请使用 azureml.core.authentication.AzureCliAuthentication__ 类。 对于 Azure CLI 身份验证，请在安装了 azureml-sdk 的 Python 环境中执行 pip install azure-cli__。
    + 如果在使用服务主体执行“az login”以实现自动化，则建议使用 azureml.core.authentication.ServicePrincipalAuthentication__ 类，因为 azureml-sdk 不会使用 azure CLI 所创建的凭据状态。

+ **Bug 修复**：此版本主要包含次要 Bug 修补程序

### <a name="azure-machine-learning-data-prep-sdk-v108"></a>Azure 机器学习数据准备 SDK v1.0.8

+ **Bug 修复**
  + 改进了获取数据配置文件的性能。
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

+ **Bug 修复**：此版本主要包含次要 Bug 修补程序

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
+ **Bug 修复**：此版本主要包含次要 Bug 修补程序

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

## <a name="2018-12-04-general-availability"></a>2018-12-04：公开上市

Azure 机器学习现已推出正式版。

### <a name="azure-machine-learning-compute"></a>Azure 机器学习计算
在此版本中，我们将宣告一个通过 [Azure 机器学习计算](how-to-set-up-training-targets.md#amlcompute)获得的全新托管计算体验。 此计算目标替换适用于 Azure 机器学习的 Azure Batch AI 计算。

此计算目标：
+ 用于模型训练和批量推理/评分
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

+ **新建**
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
  * 如果原始数据流没有名称，则随机拆分将不再失败

+ **详细信息**
  * [Azure 机器学习数据准备 SDK](https://aka.ms/data-prep-sdk)

### <a name="docs-and-notebooks"></a>文档和笔记本
+ ML 管道
  + 全新的和更新的笔记本，适用于管道、批量范围确定和样式传输示例的入门：https://aka.ms/aml-pipeline-notebooks
  + 了解如何[创建第一个管道](how-to-create-your-first-pipeline.md)
  + 了解如何[使用管道运行批量预测](how-to-use-parallel-run-step.md)
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
  * azureml.train.widgets 命名空间已移至 azureml.widgets****。
  * azureml.core.compute.AmlCompute 弃用了以下类 - azureml.core.compute.BatchAICompute 和 azureml.core.compute.DSVMCompute******。 后续版本中将删除后一类。 AmlCompute 类现在有一个更简单的定义，只需要 vm_size 和 max_nodes，并在提交作业时自动将集群从 0 扩展到 max_nodes。 我们的[示例笔记本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training)已更新该信息，并且会提供使用示例。 我们希望你喜欢这种简化以及即将发布的版本中更多令人兴奋的功能！

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
Azure 机器学习的 Azure 门户已做出以下更新：
  * 针对已发布的管道提供了一个新的“管道”**** 选项卡。
  * 增加了以下支持：将现有 HDInsight 群集附加为计算目标。

### <a name="azure-machine-learning-sdk-for-python-v0174"></a>适用于 Python 的 Azure 机器学习 SDK v0.1.74

+ **重大更改**
  * *Workspace.compute_targets、datastores、experiments、images、models 和 webservices 现在是属性而不是方法。** 例如，将 Workspace.compute_targets() 替换为 Workspace.compute_targets****。
  * *Run.get_context* 弃用了 *Run.get_submitted_run*。 后续版本中将删除后一种方法。
  * *PipelineData* 类现在需要使用数据存储对象作为参数而非需要 datastore_name。 类似地，*Pipeline* 接受 default_datastore 而非 default_datastore_name。

+ **新功能**
  * Azure 机器学习管道[示例 Notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines) 现在使用 MPI 步骤。
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

+ **已修复 bug**
  * Linux 上现在可以正确处理 64 位无符号整数溢出
  * 修复了 smart_read 中纯文本文件的不正确文本标签
  * 字符串列类型现在显示在指标视图中
  * 类型计数已修复，它现在显示映射到单个而非各个 FieldType 的 ValueKinds
  * 将路径提供为字符串时，Write_to_csv 不再失败
  * 使用 Replace 时，使 "查找" 空白将不再失败

## <a name="2018-10-12"></a>2018-10-12

### <a name="azure-machine-learning-sdk-for-python-v0168"></a>适用于 Python 的 Azure 机器学习 SDK v0.1.68

+ **新功能**
  * 创建新工作区时的多租户支持。

+ **已修复 bug**
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

+ **自动机器学习**
  * 改进了错误处理和文档
  * 修复了运行属性检索性能问题。
  * 修复了连续运行的问题。
  * 修复了 :::no-loc text="ensembling"::: 迭代问题。
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

+ **Bug 已修复：**
  * 适用于所有 Tornado 版本，无需降级 Tornado 版本
  * 值对所有值都有意义，而不仅仅是前三个值

## <a name="2018-09-public-preview-refresh"></a>2018-09（公共预览版刷新）

Azure 机器学习的新的、刷新的版本：阅读有关此版本的详细信息：https://azure.microsoft.com/blog/what-s-new-in-azure-machine-learning-service/


## <a name="next-steps"></a>后续步骤

阅读 [Azure 机器学习](overview-what-is-azure-ml.md)的概述。
