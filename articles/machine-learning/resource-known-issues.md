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
ms.openlocfilehash: 2db7a25f3f463e9210544354395c9d33a75f633c
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619377"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning"></a>Azure 机器学习的已知问题和故障排除

本文可帮助您查找和更正使用 Azure 机器学习时可能遇到的错误或故障。

## <a name="diagnostic-logs"></a>诊断日志

如果在请求帮助时可以提供诊断信息，有时会很有帮助。 要查看某些日志： 
1. 访问[Azure 机器学习工作室](https://ml.azure.com)。 
1. 在左侧，选择 **"实验"** 
1. 选择实验。
1. 选择运行。
1. 在顶部，选择 **"输出 " 日志**。

> [!NOTE]
> Azure 机器学习在训练期间记录会从各种源（例如，运行训练作业的 AutoML 或 Docker 容器）记录信息。 其中的许多日志没有详细的阐述。 如果遇到问题且联系了 Microsoft 支持部门，他们可以在排除故障时使用这些日志。


## <a name="resource-quotas"></a>资源配额

了解使用 Azure 机器学习时可能遇到的[资源配额](how-to-manage-quotas.md)。

## <a name="installation-and-import"></a>安装和导入

* **错误消息：无法卸载 'PyYAML'**

    用于 Python 的 Azure 机器学习 SDK：PyYAML 是一个`distutils`已安装的项目。 因此，在部分卸载的情况下，我们无法准确确定哪些文件属于它。 若要在忽略此错误的同时继续安装 SDK，请使用：
    
    ```Python
    pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
    ```

* **安装包时数据砖块故障**

    安装更多包时，Azure Databricks 上的 Azure 机器学习 SDK 安装失败。 某些包（如 `psutil`）可能会导致冲突。 为了避免安装错误，请通过冻结库版本来安装包。 此问题与 Databricks 相关，而与 Azure 机器学习 SDK 无关。 使用其他库时也可能会遇到此问题。 示例：
    
    ```python
    psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
    ```

    或者，如果一直面临 Python 库的安装问题，可以使用初始化脚本。 此方法并不正式受到支持。 有关详细信息，请参阅[群集范围的初始化脚本](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts)。

* **数据砖块导入错误：无法从"pandas._libs.tslibs"导入名称"Timedelta"：** 如果您在使用自动机器学习时看到此错误，请运行笔记本中的以下两行：
    ```
    %sh rm -rf /databricks/python/lib/python3.7/site-packages/pandas-0.23.4.dist-info /databricks/python/lib/python3.7/site-packages/pandas
    %sh /databricks/python/bin/pip install pandas==0.23.4
    ```

* **数据砖块导入错误：没有名为"pandas.core.indexes"的模块**：如果您在使用自动机器学习时看到此错误：

    1. 请运行以下命令，在 Azure Databricks 群集中安装两个包：
    
       ```bash
       scikit-learn==0.19.1
       pandas==0.22.0
       ```
    
    1. 分离群集，然后将其重新附加到笔记本。
    
    如果这些步骤无法解决问题，请尝试重启群集。

* **数据块 FailtoSendFeather：** 如果在读取`FailToSendFeather`Azure 数据块群集上的数据时看到错误，请参阅以下解决方案：
    
    * 将 `azureml-sdk[automl]` 包升级到最新版本。
    * 添加 `azureml-dataprep` 版本 1.1.8 或更高版本。
    * 添加 `pyarrow` 版本 0.11 或更高版本。

## <a name="create-and-manage-workspaces"></a>创建和管理工作区

> [!WARNING]
> 不支持将 Azure 机器学习工作区移动到其他订阅，或将所属订阅移动到新租户。 这样做可能会导致错误。

* **Azure 门户**：如果直接从 SDK 或门户的共享链接查看工作区，则将无法在扩展中查看包含订阅信息的正常**概述**页面。 也将无法切换到另一个工作区。 如果需要查看其他工作区，直接转到[Azure 机器学习工作室](https://ml.azure.com)并搜索工作区名称。

## <a name="set-up-your-environment"></a>设置你的环境

* **创建 AmlCompute 时遇到问题**：在 GA 发布之前从 Azure 门户创建 Azure 机器学习工作区的某些用户可能无法在该工作区中创建 AmlCompute，这一的可能性很小。 您可以针对服务提出支持请求，或者通过门户或 SDK 创建新工作区，以便立即解除阻止自己。

## <a name="work-with-data"></a>处理数据

### <a name="overloaded-azurefile-storage"></a>AzureFile 存储过载

如果收到 `Unable to upload project files to working directory in AzureFile because the storage is overloaded` 错误，请应用以下解决方法。

如果对其他工作负荷（例如数据传输）使用文件共享，则我们建议使用 Blob，以便可以自由使用文件共享来提交运行。 还可以在两个不同的工作区之间拆分工作负荷。

### <a name="passing-data-as-input"></a>将数据作为输入传递

*  **类型错误：文件未找到：没有此类文件或目录**：如果您提供的文件路径不在文件所在的位置，则会发生此错误。 需确保引用文件的方式与在计算目标上将数据集装载到的位置相一致。 为确保确定性状态，我们建议在将数据集装载到计算目标时使用抽象路径。 例如，在以下代码中，我们将数据集装载到计算目标文件系统的根目录 `/tmp` 下。 
    
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

* **较长的计算准备时间**：当您第一次连接到或创建计算目标时，可能需要几分钟甚至更长时间。 

## <a name="train-models"></a>训练模型

* **模块错误（未命名模块）：** 如果在 Azure ML 中提交实验时遇到模块错误，则意味着训练脚本希望安装包，但不会添加。 提供包名称后，Azure ML 将在用于定型运行的环境中安装包。 

    如果使用[估算器](concept-azure-machine-learning-architecture.md#estimators)提交试验，则可以根据要从哪个源安装包，通过估算器中的 `pip_packages` 或 `conda_packages` 参数指定包名称。 还可以使用 `conda_dependencies_file` 指定包含所有依赖项的 yml 文件，或使用 `pip_requirements_file` 参数列出 txt 文件中的所有 pip 要求。 如果自己的 Azure ML 环境对象要覆盖估计器使用的默认映像，则可以通过估计器构造函数的`environment`参数指定该环境。

    Azure ML 还提供适用于 Tensorflow、PyTorch、Chainer 和 SKLearn 的框架特定的估算器。 使用这些估计器将确保核心框架依赖项代表您安装在用于培训的环境中。 可以使用相应的选项根据前面所述指定额外的依赖项。 
 
    可以在 [AzureML 容器](https://github.com/Azure/AzureML-Containers)中看到 Azure ML 维护的 Docker 映像及其内容。
    框架特定的依赖项列在相应的框架文档中 - [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py#remarks)、[PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py#remarks)、[TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py#remarks)、[SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py#remarks)。

    > [!Note]
    > 如果你认为某个特定的包比较常用，需要添加到 Azure ML 维护的映像和环境中，请在 [AzureML 容器](https://github.com/Azure/AzureML-Containers)中提出 GitHub 问题。 
 
* **NameError（未定义名称）、属性错误（对象没有属性）：** 此异常应来自您的训练脚本。 可以在 Azure 门户中查看日志文件，以获取有关未定义特定名称或属性错误的详细信息。 在 SDK 中，可以使用 `run.get_details()` 来查看错误消息。 这还会列出针对运行生成的所有日志文件。 请务必查看您的训练脚本，并在重新提交运行之前修复错误。 

* **Horovod 已被关闭**：在大多数情况下，如果您遇到"AbortedError：Horovod 已关闭"，此异常意味着导致霍罗沃德关闭的一个进程存在潜在的异常。 MPI 作业中的每个排名都会在 Azure ML 中生成专属的日志文件。 这些日志名为 `70_driver_logs`。 在分布式培训的情况下，日志名称后缀为，`_rank`以便更轻松地区分日志。 要查找导致霍罗福关闭的确切错误，请浏览所有日志文件，并在driver_log文件末尾查找`Traceback`。 其中的某个文件会指出实际的根本性异常。 

* **运行或实验删除**：实验可以通过"存档实验"按钮使用[实验.存档](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#archive--)方法，或者通过"存档实验"按钮从 Azure 机器学习工作室客户端中的"实验"选项卡视图存档。 此操作将隐藏列表查询和视图中的实验，但不会将其删除。

    当前不支持永久删除单个实验或运行。 有关删除工作区资源的详细信息，请参阅[导出或删除机器学习服务工作区数据](how-to-export-delete-data.md)。

* **指标文档太大**：Azure 机器学习对指标对象的大小有内部限制，可以从训练运行中一次记录。 如果在记录列表值指标时遇到"指标文档太大"错误，请尝试将列表拆分为较小的块，例如：

    ```python
    run.log_list("my metric name", my_metric[:N])
    run.log_list("my metric name", my_metric[N:])
    ```

    在内部，Azure ML 将具有相同指标名称的块串联到连续列表中。

## <a name="automated-machine-learning"></a>自动化机器学习

* **张力流**：自动机器学习当前不支持张量流版本 1.13。 安装此版本会导致包依赖项停止工作。 我们正在努力解决此问题，以避免在将来的版本中出现。

* **实验图表**：自动 ML 实验迭代中显示的二进制分类图表（精度召回、ROC、增益曲线等）自 4 月 12 日起在用户界面中未正确呈现。 绘制的图表目前显示相反的结果：表现更好的模型反而显示更低的结果。 我们研究解决方法。

* **数据块取消自动机器学习运行**：当您在 Azure 数据块上使用自动机器学习功能时，要取消运行并启动新的实验运行，请重新启动 Azure 数据块群集。

* **数据砖块>10 次自动机器学习迭代**：在自动机器学习设置中，如果具有 10 次以上的迭代，则在`show_output``False`提交运行时设置为。

* **Azure 机器学习 SDK 和自动机器学习的数据砖块小部件**：Databricks 笔记本中不支持 Azure 机器学习 SDK 小部件，因为笔记本无法解析 HTML 小部件。 可以通过在 Azure Databricks 笔记本单元中使用以下 Python 代码，在门户中查看该小组件：

    ```
    displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
    ```

## <a name="deploy--serve-models"></a>部署和提供模型

针对以下错误执行以下操作：

|错误  | 解决方法  |
|---------|---------|
|部署 Web 服务时映像生成失败     |  将"pynacl=1.2.1"作为点依赖项添加到 Conda 文件以进行映像配置       |
|`['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`     |   将部署中使用的 VM 的 SKU 更改为具有更多内存的 SKU。 |
|FPGA 故障     |  你将无法在 FPGA 上部署模型，直到已请求并获得 FPGA 配额批准为止。 若要请求访问权限，请填写配额请求表单：https://aka.ms/aml-real-time-ai       |

### <a name="updating-azure-machine-learning-components-in-aks-cluster"></a>更新 AKS 群集中的 Azure 机器学习组件

必须手动应用对 Azure Kubernetes 服务群集中安装的 Azure 机器学习组件的更新。 

可以通过从 Azure 机器学习工作区分离群集，然后将群集重新附加到工作区，来应用这些更新。 如果在群集中启用了 TLS，则重新连接群集时需要提供 TLS/SSL 证书和私钥。 

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

如果不再有 TLS/SSL 证书和私钥，或者正在使用 Azure 机器学习生成的证书，则可以通过使用`kubectl`连接到群集并检索机密`azuremlfessl`来在分离群集之前检索文件。

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
