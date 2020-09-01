---
title: 已知问题与故障排除
titleSuffix: Azure Machine Learning
description: 获取有关在 Azure 机器学习中查找和更正错误或失败的帮助。 了解已知问题、故障排除和解决方法。
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: mldocs
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: troubleshooting, contperfq4
ms.date: 08/13/2020
ms.openlocfilehash: 4dced0e0597e4df2fe215c9f4b85e3e8defd92c3
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/01/2020
ms.locfileid: "89230375"
---
# <a name="known-issues-and-troubleshooting-in-azure-machine-learning"></a>Azure 机器学习中的已知问题和故障排除

本文帮助你对使用 Azure 机器学习时可能遇到的已知问题进行故障排除。 

有关故障排除的更多信息，请参阅本文末尾的[后续步骤](#next-steps)。

> [!TIP]
> 使用 Azure 机器学习时遇到的[资源配额](how-to-manage-quotas.md)可能会导致错误或其他问题。 

## <a name="access-diagnostic-logs"></a>访问诊断日志

如果在请求帮助时可以提供诊断信息，有时会很有帮助。 若要日志，请执行以下操作： 
1. 访问 [Azure 机器学习工作室](https://ml.azure.com)。 
1. 在左侧选择“试验” 
1. 选择一个试验。
1. 选择一个运行。
1. 在顶部，选择“输出 + 日志”。

> [!NOTE]
> Azure 机器学习在训练期间记录会从各种源（例如，运行训练作业的 AutoML 或 Docker 容器）记录信息。 其中的许多日志没有详细的阐述。 如果遇到问题且联系了 Microsoft 支持部门，他们可以在排除故障时使用这些日志。


## <a name="installation-and-import"></a>安装和导入
                           
* Pip 安装：依赖项不保证与单行安装一致： 

   这是 pip 的已知限制，因为作为单行安装时，pip 没有有效的依赖项解析程序。 它仅查看第一个独特依赖项。 

   在以下代码中，`azureml-datadrift` 和`azureml-train-automl` 都使用单行 pip 进行安装。 
     ```
       pip install azureml-datadrift, azureml-train-automl
     ```
   在本例中，假设 `azureml-datadrift` 要求版本高于 1.0，`azureml-train-automl` 要求版本低于 1.2。 如果 `azureml-datadrift` 的最新版本是 1.3，那么两个包都会升级到 1.3，即使 `azureml-train-automl` 包要求使用较旧版本。 

   若要确保为包安装适当的版本，请使用多行安装，如以下代码中所示。 在这里，顺序不是问题，因为 pip 显式降级为下一行调用的一部分。 因此，会应用适当的版本依赖项。
    
     ```
        pip install azureml-datadrift
        pip install azureml-train-automl 
     ```
     
* **安装 azureml-train-automl-client 时不保证安装解释包：** 
   
   在启用模型解释的情况下运行远程 AutoML 时，将看到错误消息“请安装 azureml-explain-model 包以获取模型解释”。 这是已知问题。 作为解决方法，请执行以下步骤之一：
  
  1. 在本地安装 azureml-explain-model。
   ```
      pip install azureml-explain-model
   ```
  2. 通过在 AutoML 配置中传递 model_explainability=False，完全禁用可解释性功能。
   ```
      automl_config = AutoMLConfig(task = 'classification',
                             path = '.',
                             debug_log = 'automated_ml_errors.log',
                             compute_target = compute_target,
                             run_configuration = aml_run_config,
                             featurization = 'auto',
                             model_explainability=False,
                             training_data = prepped_data,
                             label_column_name = 'Survived',
                             **automl_settings)
    ``` 
    
* Panda 错误：通常在 AutoML 试验期间出现：
   
   当使用 pip 手动设置环境时，你可能会注意到由于安装了不支持的包版本而导致的属性错误（特别是来自 pandas 的错误）。 若要防止此类错误，[请使用 automl_setup.cmd 安装 AutoML SDK](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/README.md)：
   
    1. 打开 Anaconda 提示符并克隆一组示例笔记本的 GitHub 存储库。

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```
    
    2. cd 到 how-to-use-azureml/automated-machine-learning 文件夹，其中提取了示例笔记本，然后运行：
    
    ```bash
    automl_setup
    ```
    
* **KeyError：在本地计算或 Azure Databricks 群集上运行 AutoML 时的 "品牌"**

    如果新环境是在2020年6月10日之后使用 SDK 1.7.0 或更早版本创建的，则在 py-cpuinfo 包中进行更新后，训练可能会因此错误而失败。  (在2020年6月10日或之前创建的环境不受影响，因为使用缓存的培训图像，所以试验在远程计算上运行。 ) 若要解决此问题，请执行以下两个步骤之一：
    
    * 将 SDK 版本更新为1.8.0 或更高版本 (这还会将 py cpuinfo 降级到 5.0.0) ：
    
      ```bash
      pip install --upgrade azureml-sdk[automl]
      ```
    
    * 将 py-cpuinfo 的已安装版本降级为5.0.0：
    
      ```bash
      pip install py-cpuinfo==5.0.0
      ```
  
* 错误消息：无法卸载 'PyYAML'

    适用于 Python 的 Azure 机器学习 SDK：PyYAML 是 `distutils` 安装的项目。 因此，在部分卸载的情况下，我们无法准确确定哪些文件属于它。 若要在忽略此错误的同时继续安装 SDK，请使用：
    
    ```Python
    pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
    ```

* **Azure 机器学习 SDK 安装失败，出现异常： ModuleNotFoundError：没有名为 "ruamel" 或 "ImportError：没有名为 ruamel. yaml 的模块"**
   
   在适用于 Python 的 Azure 机器学习 SDK 的所有发行版本的 conda 基本环境中，在基本环境中安装适用于 Python 的 Azure 机器学习 SDK for)  ( Python 时遇到此问题。 请参阅以下解决方法：

    * 避免在 conda 基本环境上安装 Python SDK，而是在新创建的用户环境上创建 conda 环境并安装 SDK。 最新的 pip 应在这个新的 conda 环境中工作。

    * 若要在 docker 中创建映像，而不能脱离 conda 基本环境，请在 docker 文件中固定 pip<= 20.1.1。

    ```Python
    conda install -c r -y conda python=3.6.2 pip=20.1.1
    ```
    
* 安装包时 Databricks 失败

    安装更多包时，Azure Databricks 上的 Azure 机器学习 SDK 安装失败。 某些包（如 `psutil`）可能会导致冲突。 为了避免安装错误，请通过冻结库版本来安装包。 此问题与 Databricks 相关，而与 Azure 机器学习 SDK 无关。 使用其他库时也可能会遇到此问题。 示例：
    
    ```python
    psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
    ```

    或者，如果一直面临 Python 库的安装问题，可以使用初始化脚本。 此方法并不正式受到支持。 有关详细信息，请参阅[群集范围的初始化脚本](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts)。

* **Databricks 导入错误：无法 `Timedelta` 从 `pandas._libs.tslibs` 导入名称**：如果你在使用自动机器学习时看到此错误，请在笔记本中运行以下两行：
    ```
    %sh rm -rf /databricks/python/lib/python3.7/site-packages/pandas-0.23.4.dist-info /databricks/python/lib/python3.7/site-packages/pandas
    %sh /databricks/python/bin/pip install pandas==0.23.4
    ```

* Databricks 导入错误：没有名为“pandas.core.indexes”的模块：如果在使用自动化机器学习时看到此错误：

    1. 请运行以下命令，在 Azure Databricks 群集中安装两个包：
    
       ```bash
       scikit-learn==0.19.1
       pandas==0.22.0
       ```
    
    1. 分离群集，然后将其重新附加到笔记本。
    
    如果这些步骤无法解决问题，请尝试重启群集。

* **Databricks FailToSendFeather**:如果在 Azure Databricks 群集上读取数据时出现 `FailToSendFeather` 错误，请参考以下解决方法：
    
    * 将 `azureml-sdk[automl]` 包升级到最新版本。
    * 添加 `azureml-dataprep` 版本 1.1.8 或更高版本。
    * 添加 `pyarrow` 版本 0.11 或更高版本。
    
## <a name="create-and-manage-workspaces"></a>创建和管理工作区

> [!WARNING]
> 不支持将 Azure 机器学习工作区移动到另一个订阅，或将拥有的订阅移到新租户。 这样做可能会导致错误。

* **Azure 门户**：如果直接通过 SDK 或门户的共享链接查看工作区，则将无法在扩展程序中查看包含订阅信息的常规“概述”页。 也将无法切换到另一个工作区。 如果需要查看其他工作区，请直接转到 [Azure 机器学习工作室](https://ml.azure.com)并搜索工作区名称。

* **Azure 机器学习工作室 Web 门户支持的浏览器**：建议使用与操作系统兼容的最新浏览器。 支持以下浏览器：
  * Microsoft Edge（新的 Microsoft Edge（最新版）， 不是旧版 Microsoft Edge）
  * Safari（最新版本，仅限 Mac）
  * Chrome（最新版本）
  * Firefox（最新版本）

## <a name="set-up-your-environment"></a>设置你的环境

* **创建 AmlCompute 时出错**：如果用户在 GA 发布之前已通过 Azure 门户创建了自己的 Azure 机器学习工作区，则他们很可能无法在该工作区中创建 AmlCompute。 可对服务提出支持请求，也可通过门户或 SDK 创建新的工作区以立即解除锁定。

* **Azure 容器注册表当前不支持资源组名称中的 unicode 字符**：由于 ACR 请求的资源组名称包含 unicode 字符，因此可能会失败。 若要缓解此问题，我们建议在具有不同名称的资源组中创建一个 ACR。

## <a name="work-with-data"></a>处理数据

### <a name="overloaded-azurefile-storage"></a>AzureFile 存储过载

如果收到 `Unable to upload project files to working directory in AzureFile because the storage is overloaded` 错误，请应用以下解决方法。

如果对其他工作负荷（例如数据传输）使用文件共享，则我们建议使用 Blob，以便可以自由使用文件共享来提交运行。 还可以在两个不同的工作区之间拆分工作负荷。

### <a name="passing-data-as-input"></a>作为输入传递数据

*  **TypeError：FileNotFound:无此类文件或目录：** 如果文件不在提供的文件路径中，则会出现此错误。 需确保引用文件的方式与在计算目标上将数据集装载到的位置相一致。 为确保确定性状态，我们建议在将数据集装载到计算目标时使用抽象路径。 例如，在以下代码中，我们将数据集装载到计算目标文件系统的根目录 `/tmp` 下。 
    
    ```python
    # Note the leading / in '/tmp/dataset'
    script_params = {
        '--data-folder': dset.as_named_input('dogscats_train').as_mount('/tmp/dataset'),
    } 
    ```

    如果不包含前导正斜杠“/”，则需要为计算目标上的工作目录添加前缀（例如 `/mnt/batch/.../tmp/dataset`），以指示要将数据集装载到的位置。

### <a name="data-labeling-projects"></a>数据标签项目

|问题  |解决方法  |
|---------|---------|
|只能使用在 Blob 数据存储中创建的数据集。     |  这是当前版本的已知限制。       |
|创建后，项目长时间显示“正在初始化”。     | 手动刷新页面。 初始化应该按每秒大约 20 个数据点的速率继续。 缺少 autorefresh 是一个已知问题。         |
|查看映像时，最近添加标签的映像不显示。     |   若要加载所有带标签的映像，请选择“第一个”按钮。 按下“第一个”按钮会返回到列表的最前面，但会加载所有带标签的数据。      |
|在为对象检测提供标记时按 Esc 键会在左上角创建大小为零的标签。 在此状态下提交标签会失败。     |   单击标签旁边的打叉标记来删除该标签。  |

### <a name="data-drift-monitors"></a><a name="data-drift"></a> 数据偏移监视器

数据偏移监视器的限制和已知问题：

* 分析历史数据时的时间范围限制为监视器频率设置的 31 个间隔。 
* 除非未指定特征列表（使用所有特征），否则特征限制为 200 个。
* 计算大小必须足够大才能处理数据。
* 确保数据集包含处于给定监视器运行的开始和结束日期范围内的数据。
* 数据集监视器仅适用于包含 50 行或更多行的数据集。
* 数据集中的列或特征根据下表中的条件划分为分类值或数字值。 如果特征不满足这些条件 - 例如，某个字符串类型的列包含 100 个以上的唯一值 - 则会从数据偏移算法中删除该特征，但仍会对其进行分析。 

    | 特征类型 | 数据类型 | 条件 | 限制 | 
    | ------------ | --------- | --------- | ----------- |
    | 分类 | string、bool、int、float | 特征中的唯一值数小于 100，并小于行数的 5%。 | Null 被视为其自身的类别。 | 
    | 数值 | int、float | 特征中的值为数字数据类型，且不符合分类特征的条件。 | 如果 15% 以上的值为 null，则会删除特征。 | 

* 如果已 [创建数据偏移监视器](how-to-monitor-datasets.md) ，但在 Azure 机器学习 studio 中看不到数据 **集监视器** 页上的数据，请尝试以下。

    1. 检查是否已在页面顶部选择了正确的日期范围。  
    1. 在“数据集监视器”选项卡上，选择试验链接以检查运行状态。  此链接位于表的最右侧。
    1. 如果运行已成功完成，请检查驱动程序日志，以便查看已生成的指标数，或者查看是否有任何警告消息。  单击试验后，在“输出 + 日志”选项卡中查找驱动程序日志。

* 如果 SDK `backfill()` 函数未生成预期的输出，则可能是由于身份验证问题。  创建要传入到此函数中的计算时，请勿使用 `Run.get_context().experiment.workspace.compute_targets`，  而应使用 [ServicePrincipalAuthentication](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.serviceprincipalauthentication?view=azure-ml-py)（例如以下代码）来创建要传入到该 `backfill()` 函数中的计算： 

  ```python
   auth = ServicePrincipalAuthentication(
          tenant_id=tenant_id,
          service_principal_id=app_id,
          service_principal_password=client_secret
          )
   ws = Workspace.get("xxx", auth=auth, subscription_id="xxx", resource_group"xxx")
   compute = ws.compute_targets.get("xxx")
   ```

## <a name="azure-machine-learning-designer"></a>Azure 机器学习设计器

* 计算准备时间很长：

第一次连接或创建计算目标可能需要几分钟甚至更长的时间。 

在模型数据收集器中，数据到达 blob 存储帐户最多需要（但通常不到）10 分钟。 等待 10 分钟以确保运行下面的单元。

```python
import time
time.sleep(600)
```

* **实时终结点的日志：**

实时终结点的日志是客户数据。 对于实时终结点故障排除，可以使用以下代码来启用日志。 

有关详细信息，请参阅 [本文](https://docs.microsoft.com/azure/machine-learning/how-to-enable-app-insights#query-logs-for-deployed-models)中的监视 web 服务终结点。

```python
from azureml.core import Workspace
from azureml.core.webservice import Webservice

ws = Workspace.from_config()
service = Webservice(name="service-name", workspace=ws)
logs = service.get_logs()
```
如果有多个租户，则可能需要先添加下面的身份验证代码 `ws = Workspace.from_config()`

```python
from azureml.core.authentication import InteractiveLoginAuthentication
interactive_auth = InteractiveLoginAuthentication(tenant_id="the tenant_id in which your workspace resides")
```

## <a name="train-models"></a>训练模型

* **ModuleErrors（没有名为“xxx”的模块）** ：如果在 Azure ML 中提交试验时遇到 ModuleErrors，则表示训练脚本需要安装某个包，但并未添加该包。 你提供包名称后，Azure ML 在用于训练运行的环境中安装该包。 

    如果使用[估算器](concept-azure-machine-learning-architecture.md#estimators)提交试验，则可以根据要从哪个源安装包，通过估算器中的 `pip_packages` 或 `conda_packages` 参数指定包名称。 还可以使用 `conda_dependencies_file` 指定包含所有依赖项的 yml 文件，或使用 `pip_requirements_file` 参数列出 txt 文件中的所有 pip 要求。 如果你有自己的 Azure ML 环境对象，并且希望替代估算器使用的默认映像，则可以通过估算器构造函数的 `environment` 参数来指定该环境。

    Azure ML 还提供适用于 TensorFlow、PyTorch、Chainer 和 SKLearn 的框架特定的估算器。 使用这些估算器可确保在用于训练的环境中自动安装核心框架依赖项。 可以使用相应的选项根据前面所述指定额外的依赖项。 
 
    可以在 [AzureML 容器](https://github.com/Azure/AzureML-Containers)中看到 Azure ML 维护的 Docker 映像及其内容。
    框架特定的依赖项列在相应的框架文档中 - [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py#remarks)、[PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py#remarks)、[TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py#remarks)、[SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py#remarks)。

    > [!Note]
    > 如果你认为某个特定的包比较常用，需要添加到 Azure ML 维护的映像和环境中，请在 [AzureML 容器](https://github.com/Azure/AzureML-Containers)中提出 GitHub 问题。 
 
* **NameError（未定义名称）、AttributeError（对象没有属性）** ：此异常应该是训练脚本引发的。 可以在 Azure 门户中查看日志文件，以获取有关未定义特定名称或属性错误的详细信息。 在 SDK 中，可以使用 `run.get_details()` 来查看错误消息。 这还会列出针对运行生成的所有日志文件。 在重新提交运行之前，请务必检查训练脚本并修复错误。 

* **Horovod 已关闭**：在大多数情况下，如果遇到“AbortedError:Horovod 已关闭”，此异常表示某个进程中的根本性异常导致 Horovod 关闭。 MPI 作业中的每个排名都会在 Azure ML 中生成专属的日志文件。 这些日志名为 `70_driver_logs`。 对于分布式训练，日志名称带有 `_rank` 后缀，以方便区分日志。 若要查找导致 Horovod 关闭的确切错误，请浏览所有日志文件，并查看 driver_log 文件末尾的 `Traceback`。 其中的某个文件会指出实际的根本性异常。 

* **运行或试验删除**：可以通过以下方式将试验存档：使用 [Experiment.archive](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#archive--) 方法，或者从 Azure 机器学习工作室客户端中的“试验”选项卡视图中使用“存档试验”按钮。 执行此操作后，在列出查询和视图时将隐藏该试验，但不会将其删除。

    目前不支持永久删除个体试验或运行。 有关删除工作区资产的详细信息，请参阅[导出或删除机器学习服务工作区数据](how-to-export-delete-data.md)。

* **指标文档太大**：对于一次性可从训练运行记录的指标对象大小，Azure 机器学习施加了内部限制。 如果在记录列表值指标时遇到“指标文档太大”错误，请尝试将列表拆分为较小的区块，例如：

    ```python
    run.log_list("my metric name", my_metric[:N])
    run.log_list("my metric name", my_metric[N:])
    ```

    在内部，Azure ML 会将具有相同指标名称的块串联到一个连续列表中。

## <a name="automated-machine-learning"></a>自动化机器学习

* **最新的 AutoML 依赖项升级到较新的版本将中断 compatibilitity**：从 SDK 的版本1.13.0 开始，模型将不会加载到较旧的 sdk 中，因为我们在以前的包中固定的旧版本之间存在不兼容的情况，并且我们现在将固定较新版本。 你将看到如下错误：
  * 找不到模块：例如 `No module named 'sklearn.decomposition._truncated_svd` 。
  * 导入错误：例如 `ImportError: cannot import name 'RollingOriginValidator'` ，
  * 属性错误： Ex `AttributeError: 'SimpleImputer' object has no attribute 'add_indicator`
  
  若要解决此问题，请执行以下两个步骤之一，具体取决于你的 AutoML SDK 培训版本：
  1. 如果 AutoML SDK 培训版本高于1.13.0，则需要 `pandas == 0.25.1` 和 `sckit-learn==0.22.1` 。 如果存在版本不匹配问题，请升级 scikit-learn 和/或 pandas 以更正版本，如下所示：
  
  ```bash
     pip install --upgrade pandas==0.25.1
     pip install --upgrade scikit-learn==0.22.1
  ```
  
  2. 如果 AutoML SDK 培训版本小于或等于1.12.0，则需要 `pandas == 0.23.4` 和 `sckit-learn==0.20.3` 。 如果版本不匹配，请将 scikit-learn 和/或 pandas 降级为正确的版本，如下所示：
  
  ```bash
    pip install --upgrade pandas==0.23.4
    pip install --upgrade scikit-learn==0.20.3
  ```
 
* **TensorFlow**：从 SDK 的版本1.5.0 版，自动机器学习默认情况下不安装 TensorFlow 模型。 若要安装 TensorFlow 并将其与自动 ML 试验一起使用，请通过 CondaDependecies 安装 TensorFlow = = 1.12.0。 
 
   ```python
   from azureml.core.runconfig import RunConfiguration
   from azureml.core.conda_dependencies import CondaDependencies
   run_config = RunConfiguration()
   run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['tensorflow==1.12.0'])
  ```
* **试验图表**：自 4 月 12 日以来，自动化 ML 试验迭代中显示的二元分类图表（精准率-召回率、ROC、增益曲线等）在用户界面中无法正常呈现。 绘制的图表目前显示相反的结果：表现更好的模型反而显示更低的结果。 我们研究解决方法。

* **Databricks 取消自动化机器学习运行**：在 Azure Databricks 上使用自动化机器学习功能时，若要取消某个运行并启动新的试验运行，请重启 Azure Databricks 群集。

* **Databricks 自动化机器学习的迭代数超过 10 个**：在自动化机器学习设置中，如果迭代数超过 10 个，请在提交运行时将 `show_output` 设置为 `False`。

* **Databricks Azure 机器学习 SDK 和自动化机器学习的小组件**：Databricks 笔记本不支持 Azure 机器学习 SDK 小组件，因为笔记本无法分析 HTML 小组件。 可以通过在 Azure Databricks 笔记本单元中使用以下 Python 代码，在门户中查看该小组件：

    ```
    displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
    ```
* **automl_setup 失败**： 
    * 在 Windows 上，从 Anaconda 提示符运行 automl_setup。 若要安装 Miniconda) ，请单击 [此处](https://docs.conda.io/en/latest/miniconda.html)。
    * 通过运行命令确保安装了 conda 64 位，而不是32位 `conda info` 。 `platform`应该 `win-64` 适用于 Windows 或 `osx-64` for Mac。
    * 确保已安装 conda 4.4.10 或更高版本。 可以通过命令检查版本 `conda -V` 。 如果你安装了以前的版本，则可以使用命令进行更新： `conda update conda` 。
    * Linux `gcc: error trying to exec 'cc1plus'`
      *  如果 `gcc: error trying to exec 'cc1plus': execvp: No such file or directory` 遇到错误，请使用管理中心捆绑命令安装 build essentials `sudo apt-get install build-essential` 。
      * 将新名称作为第一个参数传递给 automl_setup 以创建新的 conda 环境。 使用查看现有的 conda 环境 `conda env list` ，并使用将其删除 `conda env remove -n <environmentname>` 。
      
* **automl_setup_linux sh 失败**：如果 automl_setup_linus，则在 Ubuntu Linux 出现错误： `unable to execute 'gcc': No such file or directory`-
  1. 确保启用出站端口53和80。 在 Azure VM 上，可以通过在 Azure 门户中选择 VM 并单击 "网络" 来执行此操作。
  2. 运行命令 `sudo apt-get update`
  3. 运行命令 `sudo apt-get install build-essential --fix-missing`
  4. `automl_setup_linux.sh`再次运行

* **ipynb 失败**：
  * 对于本地 conda，请首先确保 automl_setup 具有 susccessfully 运行。
  * 确保 subscription_id 正确。 依次选择 "所有服务" 和 "订阅"，在 Azure 门户中查找 subscription_id。 Subscription_id 值中不应包含字符 "<" 和 ">"。 例如， `subscription_id = "12345678-90ab-1234-5678-1234567890abcd"` 具有有效的格式。
  * 确保参与者或所有者对订阅的访问权限。
  * 检查该区域是否为受支持的区域之一： `eastus2` 、 `eastus` 、 `westcentralus` 、 `southeastasia` 、 `westeurope` `australiaeast` `westus2` 、、和 `southcentralus` 。
  * 使用 Azure 门户确保对区域的访问权限。
  
* **导入 AutoMLConfig 失败**：自动机器学习版本1.0.76 中存在包更改，这需要在更新到新版本之前卸载以前的版本。 如果 `ImportError: cannot import name AutoMLConfig` 从 1.0.76 v 到 v 1.0.76 或更高版本的 SDK 版本升级之后遇到此错误，请通过运行以下内容来解决该错误： `pip uninstall azureml-train automl` 和 `pip install azureml-train-auotml` 。 Automl_setup .cmd 脚本会自动执行此功能。 

* **工作区。 from_config 失败**：如果调用 Ws = workspace from_config ( # A1 "失败-
  1. 确保 ipynb 笔记本已成功运行。
  2. 如果正在运行的文件夹不在运行的文件夹下运行 `configuration.ipynb` ，请将文件夹 aml_config，并将其包含的文件 config.js到新文件夹中。 From_config 读取笔记本文件夹或其父文件夹的 config.js。
  3. 如果正在使用新的订阅、资源组、工作区或区域，请确保 `configuration.ipynb` 再次运行笔记本。 仅当工作区在指定的订阅下指定的资源组中已存在时，才能直接更改 config.js。
  4. 若要更改区域，请更改工作区、资源组或订阅。 `Workspace.create` 如果工作区已经存在，则不会创建或更新它，即使指定的区域不同也是如此。
  
* **示例笔记本失败**：如果示例笔记本失败并出现错误，preperty、方法或库不存在：
  * 确保已在 jupyter 笔记本中选择 correctcorrect 内核。 内核显示在笔记本页面的右上方。 默认值为 azure_automl。 请注意，内核将作为笔记本的一部分保存。 因此，如果切换到新的 conda 环境，则必须在笔记本中选择新内核。
      * 对于 Azure Notebooks，它应为 Python 3.6。 
      * 对于本地 conda 环境，该环境应为在 automl_setup 中指定的 conda envioronment 名称。
  * 确保笔记本适用于你正在使用的 SDK 版本。 可以通过 `azureml.core.VERSION` 在 jupyter 笔记本单元中执行来检查 SDK 版本。 您可以通过单击该 `Branch` 按钮，选择该 `Tags` 选项卡，然后选择版本来从 GitHub 下载以前版本的示例笔记本。

* **Windows 中的 Numpy 导入失败**：一些 windows 环境会看到一个错误，加载 Numpy 最新的 Python 版本3.6.8。 如果你看到此问题，请尝试 Python 版本3.6.7。

* **Numpy 导入失败**：请在自动 ml conda 环境中检查 tensorflow 版本。 支持的版本为 1.13 <。 如果版本 >为1.13，则从环境中卸载 tensorflow，可以按如下所示检查 tensorflow 和 uninstall 的版本：
  1. 启动命令行界面，激活安装了自动 ml 包的 conda 环境。
  2. 输入 `pip freeze` 并查找 `tensorflow` ，如果找到，则列出的版本应为 < 1.13
  3. 如果列出的版本不是受支持的版本，请 `pip uninstall tensorflow` 在命令外壳中输入 y 进行确认。

## <a name="deploy--serve-models"></a>部署和提供模型

对以下错误采取以下操作：

|错误  | 解决方法  |
|---------|---------|
|部署 Web 服务时映像生成失败     |  将“pynacl==1.2.1”作为 pip 依赖项添加到 Conda 文件以进行映像配置       |
|`['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`     |   请将部署中使用的 VM 的 SKU 更改为具有更多内存的 SKU。 |
|FPGA 失败     |  你将无法在 FPGA 上部署模型，直到已请求并获得 FPGA 配额批准为止。 若要请求访问权限，请填写配额请求表单： https://aka.ms/aml-real-time-ai       |

### <a name="updating-azure-machine-learning-components-in-aks-cluster"></a>更新 AKS 群集中的 Azure 机器学习组件

必须手动应用对 Azure Kubernetes 服务群集中安装的 Azure 机器学习组件的更新。 

可以通过从 Azure 机器学习工作区分离群集，然后将群集重新附加到工作区，来应用这些更新。 如果在群集中启用了 TLS，则重新附加群集时需要提供 TLS/SSL 证书和私钥。 

```python
compute_target = ComputeTarget(workspace=ws, name=clusterWorkspaceName)
compute_target.detach()
compute_target.wait_for_completion(show_output=True)

attach_config = AksCompute.attach_configuration(resource_group=resourceGroup, cluster_name=kubernetesClusterName)

## If SSL is enabled.
attach_config.enable_ssl(
    ssl_cert_pem_file="cert.pem",
    ssl_key_pem_file="key.pem",
    ssl_cname=sslCname)

attach_config.validate_configuration()

compute_target = ComputeTarget.attach(workspace=ws, name=args.clusterWorkspaceName, attach_configuration=attach_config)
compute_target.wait_for_completion(show_output=True)
```

如果不再具有 TLS/SSL 证书和私钥，或者使用 Azure 机器学习生成的证书，则可以在分离群集之前，使用 `kubectl` 连接到群集并检索机密 `azuremlfessl` 来检索文件。

```bash
kubectl get secret/azuremlfessl -o yaml
```

>[!Note]
>Kubernetes 存储的机密采用 base-64 编码格式。 在将机密提供给 `attach_config.enable_ssl` 之前，需要对机密的 `cert.pem` 和 `key.pem` 组成部分进行 base-64 解码。 

### <a name="webservices-in-azure-kubernetes-service-failures"></a>Azure Kubernetes 服务中的 Web 服务失败

对于 Azure Kubernetes 服务中的许多 Web 服务失败，可以使用 `kubectl` 连接到群集进行调试。 可以运行以下命令获取 Azure Kubernetes 服务群集的 `kubeconfig.json`

```azurecli-interactive
az aks get-credentials -g <rg> -n <aks cluster name>
```

## <a name="authentication-errors"></a>身份验证错误

如果通过远程作业对某个计算目标执行管理操作，会收到以下错误之一： 

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

例如，如果尝试通过一个为实施远程执行操作而提交的机器学习管道创建或附加计算目标，会收到错误。

## <a name="missing-user-interface-items-in-studio"></a>工作室中缺少用户界面项

可以使用 Azure 基于角色的访问控制来限制可使用 Azure 机器学习执行的操作。 这些限制可以防止用户界面项显示在 Azure 机器学习工作室中。 例如，如果分配了无法创建计算实例的角色，则创建计算实例的选项不会出现在工作室中。

有关详细信息，请参阅[管理用户和角色](how-to-assign-roles.md)。

## <a name="next-steps"></a>后续步骤

请参阅更多有关 Azure 机器学习的故障排除文章：

* [使用 Azure 机器学习解决 Docker 部署问题](how-to-troubleshoot-deployment.md)
* [调试机器学习管道](how-to-debug-pipelines.md)
* [从 Azure 机器学习 SDK 调试 ParallelRunStep 类](how-to-debug-parallel-run-step.md)
* [使用 VS Code 的机器学习计算实例进行交互式调试](how-to-debug-visual-studio-code.md)
* [使用 Application Insights 调试机器学习管道](how-to-debug-pipelines-application-insights.md)
