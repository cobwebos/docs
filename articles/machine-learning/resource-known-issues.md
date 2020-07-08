---
title: 已知问题与故障排除
titleSuffix: Azure Machine Learning
description: 获取有关在 Azure 机器学习中查找和更正错误或失败的帮助。 了解已知问题、故障排除和解决方法。
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.subservice: core
ms.topic: troubleshooting
ms.custom: contperfq4
ms.date: 03/31/2020
ms.openlocfilehash: a3e78ff2936cb3dbbc1bcf432f130fbd17622d14
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85610058"
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
  
* 错误消息：无法卸载 'PyYAML'

    适用于 Python 的 Azure 机器学习 SDK：PyYAML 是 `distutils` 安装的项目。 因此，在部分卸载的情况下，我们无法准确确定哪些文件属于它。 若要在忽略此错误的同时继续安装 SDK，请使用：
    
    ```Python
    pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
    ```

* 安装包时 Databricks 失败

    安装更多包时，Azure Databricks 上的 Azure 机器学习 SDK 安装失败。 某些包（如 `psutil`）可能会导致冲突。 为了避免安装错误，请通过冻结库版本来安装包。 此问题与 Databricks 相关，而与 Azure 机器学习 SDK 无关。 使用其他库时也可能会遇到此问题。 示例：
    
    ```python
    psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
    ```

    或者，如果一直面临 Python 库的安装问题，可以使用初始化脚本。 此方法并不正式受到支持。 有关详细信息，请参阅[群集范围的初始化脚本](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts)。

* **Databricks 导入错误：无法从“pandas._libs.tslibs”导入名称“Timedelta”** ：如果在使用自动机器学习时看到此错误，请在笔记本中运行以下两行：
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

## <a name="set-up-your-environment"></a>设置你的环境

* **创建 AmlCompute 时出错**：如果用户在 GA 发布之前已通过 Azure 门户创建了自己的 Azure 机器学习工作区，则他们很可能无法在该工作区中创建 AmlCompute。 可对服务提出支持请求，也可通过门户或 SDK 创建新的工作区以立即解除锁定。

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

### <a name="data-drift-monitors"></a>数据偏移监视器

* 如果 SDK `backfill()` 函数不生成预期的输出，则可能是由于身份验证问题所致。  创建要传入此函数的计算时，请不要使用 `Run.get_context().experiment.workspace.compute_targets` 。  相反，请使用[ServicePrincipalAuthentication](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.serviceprincipalauthentication?view=azure-ml-py) （如下所示）创建传递到该函数的计算 `backfill()` ： 

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

已知问题：

* **计算准备时间很长**：第一次连接或创建计算目标可能需要几分钟甚至更长的时间。 

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

* **TensorFlow**：从 SDK 的版本1.5.0 版，自动机器学习默认情况下不安装 TensorFlow 模型。 若要安装 tensorflow 并将其与自动 ML 试验一起使用，请通过 CondaDependecies 安装 tensorflow = = 1.12.0。 
 
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

## <a name="next-steps"></a>后续步骤

请参阅更多有关 Azure 机器学习的故障排除文章：

* [使用 Azure 机器学习解决 Docker 部署问题](how-to-troubleshoot-deployment.md)
* [调试机器学习管道](how-to-debug-pipelines.md)
* [从 Azure 机器学习 SDK 调试 ParallelRunStep 类](how-to-debug-parallel-run-step.md)
* [使用 VS Code 的机器学习计算实例进行交互式调试](how-to-set-up-vs-code-remote.md)
* [使用 Application Insights 调试机器学习管道](how-to-debug-pipelines-application-insights.md)
