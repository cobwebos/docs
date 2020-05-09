---
title: 已知问题与故障排除
titleSuffix: Azure Machine Learning
description: 获取 Azure 机器学习的已知问题列表、解决方法和故障排除方法。
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 93015da810f163a48529704e69e1747ac1aec401
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2020
ms.locfileid: "82889400"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning"></a>Azure 机器学习的已知问题和故障排除

本文可帮助你查找和更正使用 Azure 机器学习时可能遇到的错误或故障。

## <a name="diagnostic-logs"></a>诊断日志

如果在请求帮助时可以提供诊断信息，有时会很有帮助。 查看一些日志： 
1. 访问[Azure 机器学习 studio](https://ml.azure.com)。 
1. 在左侧选择 "**试验**" 
1. 选择一个试验。
1. 选择 "运行"。
1. 在顶部，选择 "**输出 + 日志**"。

> [!NOTE]
> Azure 机器学习在训练期间记录会从各种源（例如，运行训练作业的 AutoML 或 Docker 容器）记录信息。 其中的许多日志没有详细的阐述。 如果遇到问题且联系了 Microsoft 支持部门，他们可以在排除故障时使用这些日志。


## <a name="resource-quotas"></a>资源配额

了解使用 Azure 机器学习时可能遇到的[资源配额](how-to-manage-quotas.md)。

## <a name="installation-and-import"></a>安装和导入
                           
* **Pip 安装：不保证依赖项与单行安装一致**： 

   这是 pip 的已知限制，因为当你作为单行安装时，它不具有正常运行的依赖关系解析程序。 第一个唯一的依赖项是唯一的依赖项。 

   在下面的代码`azure-ml-datadrift`中`azureml-train-automl` ，同时使用单行 pip 安装进行安装。 
     ```
       pip install azure-ml-datadrift, azureml-train-automl
     ```
   对于本示例，假设`azure-ml-datadrift`需要版本 > 1.0，并且`azureml-train-automl`需要版本 < 1.2。 如果的`azure-ml-datadrift`最新版本为1.3，则这两个包都将升级到1.3， `azureml-train-automl`而与较旧版本的包要求无关。 

   若要确保为包安装适当的版本，请使用多行安装，如以下代码所示。 此处的顺序并不是问题，因为 pip 在下一行调用中显式降级。 因此，会应用适当的版本依赖项。
    
     ```
        pip install azure-ml-datadrift
        pip install azureml-train-automl 
     ```
     
* **说明安装 guarateed 时不会安装的包-automl-客户端：** 
   
   在启用了模型解释的情况运行远程 automl 运行时，将看到一条错误消息，指出 "" 请安装 azureml-模型说明的模型包。 " 这是一个已知问题，作为一种解决方法，请执行以下步骤之一：
  
  1. 在本地安装 azureml-说明模型。
   ```
      pip install azureml-explain-model
   ```
  2. 通过在 automl 配置中传递 model_explainability = False 来完全禁用 explainability 功能。
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
    
* **Panda 错误：通常在 AutoML 试验期间出现：**
   
   使用 pip 手动设置 environmnet 时，你会发现属性错误（尤其是从 pandas），因为安装了不受支持的包版本。 若要防止此类错误，[请使用 automl_setup 命令安装 AUTOML SDK](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/README.md)：
   
    1. 打开 Anaconda 提示符并克隆适用于一组示例笔记本的 GitHub 存储库。

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```
    
    2. cd 转到示例笔记本在其中提取并运行的 "使用 azureml/自动计算机" 文件夹：
    
    ```bash
    automl_setup
    ```
  
* **错误消息：无法卸载 'PyYAML'**

    适用于 Python 的 Azure 机器学习 SDK： PyYAML `distutils`是一个已安装的项目。 因此，在部分卸载的情况下，我们无法准确确定哪些文件属于它。 若要在忽略此错误的同时继续安装 SDK，请使用：
    
    ```Python
    pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
    ```

* **安装包时出现 Databricks 失败**

    安装更多包时，Azure Databricks 上的 Azure 机器学习 SDK 安装失败。 某些包（如 `psutil`）可能会导致冲突。 为了避免安装错误，请通过冻结库版本来安装包。 此问题与 Databricks 相关，而与 Azure 机器学习 SDK 无关。 使用其他库时也可能会遇到此问题。 示例：
    
    ```python
    psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
    ```

    或者，如果一直面临 Python 库的安装问题，可以使用初始化脚本。 此方法并不正式受到支持。 有关详细信息，请参阅[群集范围的初始化脚本](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts)。

* **Databricks 导入错误：无法从 "_libs pandas" 导入名称 "Timedelta"**：如果你在使用自动机器学习时看到此错误，请在笔记本中运行以下两行：
    ```
    %sh rm -rf /databricks/python/lib/python3.7/site-packages/pandas-0.23.4.dist-info /databricks/python/lib/python3.7/site-packages/pandas
    %sh /databricks/python/bin/pip install pandas==0.23.4
    ```

* **Databricks 导入错误：不存在名为 "pandas" 的模块**：如果你在使用自动机器学习时看到此错误：

    1. 请运行以下命令，在 Azure Databricks 群集中安装两个包：
    
       ```bash
       scikit-learn==0.19.1
       pandas==0.22.0
       ```
    
    1. 分离群集，然后将其重新附加到笔记本。
    
    如果这些步骤无法解决问题，请尝试重启群集。

* **Databricks FailToSendFeather**：如果在 Azure Databricks 群集`FailToSendFeather`上读取数据时出现错误，请参阅以下解决方案：
    
    * 将 `azureml-sdk[automl]` 包升级到最新版本。
    * 添加 `azureml-dataprep` 版本 1.1.8 或更高版本。
    * 添加 `pyarrow` 版本 0.11 或更高版本。
    
## <a name="create-and-manage-workspaces"></a>创建和管理工作区

> [!WARNING]
> 不支持将 Azure 机器学习工作区移动到不同的订阅，或将拥有的订阅移动到新租户。 这样做可能会导致错误。

* **Azure 门户**：如果你直接从 SDK 或门户的共享链接中查看工作区，则你将不能在扩展中查看包含订阅信息的正常**概述**页。 也将无法切换到另一个工作区。 如果需要查看其他工作区，请直接跳到[Azure 机器学习 studio](https://ml.azure.com)并搜索工作区名称。

## <a name="set-up-your-environment"></a>设置你的环境

* **创建 AmlCompute 时出现问题**：在 GA 版本之前，从 Azure 门户创建其 Azure 机器学习工作区的某些用户很少可能无法在该工作区中创建 AmlCompute。 你可以对服务提出支持请求，或者通过门户或 SDK 创建新的工作区以立即取消阻止。

## <a name="work-with-data"></a>处理数据

### <a name="overloaded-azurefile-storage"></a>AzureFile 存储过载

如果收到 `Unable to upload project files to working directory in AzureFile because the storage is overloaded` 错误，请应用以下解决方法。

如果对其他工作负荷（例如数据传输）使用文件共享，则我们建议使用 Blob，以便可以自由使用文件共享来提交运行。 还可以在两个不同的工作区之间拆分工作负荷。

### <a name="passing-data-as-input"></a>作为输入传递数据

*  **TypeError： FileNotFound：没有此类文件或目录**：如果提供的文件路径不在文件所在位置，则会出现此错误。 需确保引用文件的方式与在计算目标上将数据集装载到的位置相一致。 为确保确定性状态，我们建议在将数据集装载到计算目标时使用抽象路径。 例如，在以下代码中，我们将数据集装载到计算目标文件系统的根目录 `/tmp` 下。 
    
    ```python
    # Note the leading / in '/tmp/dataset'
    script_params = {
        '--data-folder': dset.as_named_input('dogscats_train').as_mount('/tmp/dataset'),
    } 
    ```

    如果不包含前导正斜杠“/”，则需要为计算目标上的工作目录添加前缀（例如 `/mnt/batch/.../tmp/dataset`），以指示要将数据集装载到的位置。

### <a name="data-labeling-projects"></a>数据标记项目

|问题  |解决方法  |
|---------|---------|
|只能使用在 Blob 数据存储中创建的数据集     |  这是当前版本的已知限制。       |
|创建后，项目长时间显示“正在初始化”     | 手动刷新页面。 初始化应该按每秒大约 20 个数据点的速率继续。 缺少 autorefresh 是一个已知问题。         |
|查看映像时，最近添加标签的映像不显示     |   若要加载所有带标签的映像，请选择“第一个”按钮。**** 按下“第一个”按钮会返回到列表的最前面，但会加载所有带标签的数据。****      |
|在为对象检测提供标记时按 Esc 键会在左上角创建大小为零的标签。 在此状态下提交标签会失败。     |   单击标签旁边的打叉标记来删除该标签。  |

## <a name="azure-machine-learning-designer"></a>Azure 机器学习设计器

已知问题：

* **计算准备时间较长**：首次连接或创建计算目标时，可能需要几分钟或更长时间。 

## <a name="train-models"></a>训练模型

* **ModuleErrors （没有名为的模块）**：如果在 Azure ML 中提交试验时运行在 ModuleErrors 中，则表示训练脚本需要安装一个包，但不会添加它。 提供包名称后，Azure ML 会在用于定型运行的环境中安装包。 

    如果使用[估算器](concept-azure-machine-learning-architecture.md#estimators)提交试验，则可以根据要从哪个源安装包，通过估算器中的 `pip_packages` 或 `conda_packages` 参数指定包名称。 还可以使用 `conda_dependencies_file` 指定包含所有依赖项的 yml 文件，或使用 `pip_requirements_file` 参数列出 txt 文件中的所有 pip 要求。 如果你有自己的 Azure ML 环境对象，而你想要重写估计器使用的默认映像，则可以通过估计器构造函数的`environment`参数指定该环境。

    Azure ML 还提供适用于 Tensorflow、PyTorch、Chainer 和 SKLearn 的框架特定的估算器。 使用这些估算将确保在用于定型的环境中代表您安装核心框架依赖项。 可以使用相应的选项根据前面所述指定额外的依赖项。 
 
    可以在 [AzureML 容器](https://github.com/Azure/AzureML-Containers)中看到 Azure ML 维护的 Docker 映像及其内容。
    框架特定的依赖项列在相应的框架文档中 - [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py#remarks)、[PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py#remarks)、[TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py#remarks)、[SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py#remarks)。

    > [!Note]
    > 如果你认为某个特定的包比较常用，需要添加到 Azure ML 维护的映像和环境中，请在 [AzureML 容器](https://github.com/Azure/AzureML-Containers)中提出 GitHub 问题。 
 
* **NameError （未定义名称）、AttributeError （对象没有属性）**：此异常应来自定型脚本。 可以在 Azure 门户中查看日志文件，以获取有关未定义特定名称或属性错误的详细信息。 在 SDK 中，可以使用 `run.get_details()` 来查看错误消息。 这还会列出针对运行生成的所有日志文件。 请确保在重新提交运行之前，查看训练脚本并修复错误。 

* **Horovod 已**关闭：在大多数情况下，如果遇到 "AbortedError： Horovod 已关闭" 这一异常，则表示某个进程中存在导致 Horovod 关闭的基础异常。 MPI 作业中的每个排名都会在 Azure ML 中生成专属的日志文件。 这些日志名为 `70_driver_logs`。 对于分布式培训，日志名称的后缀`_rank`为，以便更容易区分日志。 若要查找导致 Horovod 关闭的确切错误，请浏览所有日志文件，并查找`Traceback` driver_log 文件的末尾。 其中的某个文件会指出实际的根本性异常。 

* **运行或试验删除**：可以通过使用[试验. archive](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#archive--)方法或通过 "存档试验" 按钮从 Azure 机器学习 studio 客户端中的实验选项卡视图存档试验。 此操作将从列表查询和视图中隐藏实验，但不会将其删除。

    当前不支持永久删除各个试验或运行。 有关删除工作区资产的详细信息，请参阅[导出或删除机器学习服务工作区数据](how-to-export-delete-data.md)。

* **指标文档太大**： Azure 机器学习对指标对象的大小进行了内部限制，可通过训练运行一次进行记录。 如果在记录列表值指标时遇到 "指标文档太大" 错误，请尝试将列表拆分为较小的区块，例如：

    ```python
    run.log_list("my metric name", my_metric[:N])
    run.log_list("my metric name", my_metric[N:])
    ```

    在内部，Azure ML 会将具有相同指标名称的块连接到一个连续列表中。

## <a name="automated-machine-learning"></a>自动化机器学习

* **Tensor flow**：自动化机器学习当前不支持 Tensor 流版本1.13。 安装此版本会导致包依赖项停止工作。 我们正在努力解决此问题，以避免在将来的版本中出现。

* **试验图**：自4/12 以来，自动 ML 试验迭代中显示的二进制分类图表（精度召回、ROC、增益曲线等）在用户界面中无法正确呈现。 绘制的图表目前显示相反的结果：表现更好的模型反而显示更低的结果。 我们研究解决方法。

* **Databricks 取消自动机器学习运行**：在 Azure Databricks 上使用自动机器学习功能时，若要取消运行并启动新的试验运行，请重新启动 Azure Databricks 群集。

* **Databricks 为自动机器学习 >10 次迭代**：在自动机器学习设置中，如果有10个以上的迭代`show_output` ， `False`请在提交运行时将设置为。

* **用于 AZURE 机器学习 sdk 和自动机器学习的 Databricks 小组件**：在 Databricks 笔记本中不支持 Azure 机器学习 SDK 小组件，因为笔记本无法解析 HTML 小组件。 可以通过在 Azure Databricks 笔记本单元中使用以下 Python 代码，在门户中查看该小组件：

    ```
    displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
    ```

## <a name="deploy--serve-models"></a>部署和提供模型

执行以下错误操作：

|错误  | 解决方法  |
|---------|---------|
|部署 web 服务时生成图像失败     |  将 "pynacl = = 1.2.1" 添加为映像配置的 Conda 文件的 pip 依赖项       |
|`['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`     |   将部署中使用的 Vm 的 SKU 更改为具有更多内存的虚拟机。 |
|FPGA 失败     |  你将无法在 FPGA 上部署模型，直到已请求并获得 FPGA 配额批准为止。 若要请求访问权限，请填写配额请求表单：https://aka.ms/aml-real-time-ai       |

### <a name="updating-azure-machine-learning-components-in-aks-cluster"></a>更新 AKS 群集中的 Azure 机器学习组件

必须手动应用对 Azure Kubernetes 服务群集中安装的 Azure 机器学习组件的更新。 

可以通过从 Azure 机器学习工作区分离群集，然后将群集重新附加到工作区，来应用这些更新。 如果在群集中启用了 TLS，则需要在重新附加群集时提供 TLS/SSL 证书和私钥。 

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

如果不再具有 TLS/SSL 证书和私钥，或者使用的是 Azure 机器学习生成的证书，则可以在分离群集之前通过使用`kubectl`和检索机密`azuremlfessl`来连接到该群集来检索这些文件。

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
