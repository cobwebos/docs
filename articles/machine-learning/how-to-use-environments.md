---
title: 创建可重用的 ML 环境
titleSuffix: Azure Machine Learning
description: 创建和管理用于模型训练和部署的环境。 管理环境的 Python 包和其他设置。
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: dc30318027962247f7504b734385d7642550ec87
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536346"
---
# <a name="reuse-environments-for-training-and-deployment-by-using-azure-machine-learning"></a>使用 Azure 机器学习重用用于培训和部署的环境
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，了解如何创建和管理 Azure 机器学习[环境](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)。 使用环境跟踪和重现项目的软件依赖关系，并随着项目的发展而变化。

软件依赖项管理是开发人员的常见任务。 您希望确保在未进行大量手动软件配置的情况下可重现生成。 Azure 机器学习`Environment`类考虑本地开发解决方案（如 pip 和 Conda），它为本地和分布式云开发提供了解决方案。

本文中的示例演示如何执行以下操作：

* 创建环境并指定包依赖项。
* 检索和更新环境。
* 使用环境进行培训。
* 使用环境进行 Web 服务部署。

有关环境在 Azure 机器学习中工作方式的高级概述，请参阅什么是[ML 环境？](concept-environments.md)

## <a name="prerequisites"></a>先决条件

* [用于 Python 的 Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* [Azure 机器学习工作区](how-to-manage-workspace.md)

## <a name="create-an-environment"></a>创建环境

以下各节探讨了为实验创建环境的多种方法。

### <a name="use-a-curated-environment"></a>使用精心策划的环境

您可以选择一个精心策划的环境，以便从以下开始： 

* _AzureML-Minimal_ 环境包含最小的一组包，用于启用运行跟踪和资产上传。 在创建自己的环境时，可以将其作为起点。

* _AzureML-教程_环境包含通用的数据科学包。 这些包包括 Scikit-学习、熊猫、Matplotlib 和一组更大的 azureml-sdk 包。

精选环境由缓存的 Docker 映像支持。 此备份降低了运行准备成本。

使用`Environment.get`方法选择其中一个已计算的环境：

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
env = Environment.get(workspace=ws, name="AzureML-Minimal")
```

您可以使用以下代码列出已策划的环境及其包：

```python
envs = Environment.list(workspace=ws)

for env in envs:
    if env.startswith("AzureML"):
        print("Name",env)
        print("packages", envs[env].python.conda_dependencies.serialize_to_string())
```

> [!WARNING]
>  不要使用_AzureML_前缀启动自己的环境名称。 此前缀保留给已策划的环境。

### <a name="instantiate-an-environment-object"></a>实例化环境对象

要手动创建环境，`Environment`请从 SDK 导入类。 然后使用以下代码实例化环境对象。

```python
from azureml.core.environment import Environment
Environment(name="myenv")
```

### <a name="use-conda-and-pip-specification-files"></a>使用康达和点规格文件

还可以通过 Conda 规范或 pip 要求文件创建环境。 使用[`from_conda_specification()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-conda-specification-name--file-path-)方法或[`from_pip_requirements()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-pip-requirements-name--file-path-)方法。 在方法参数中，包括环境名称和所需的文件的文件路径。

```python
# From a Conda specification file
myenv = Environment.from_conda_specification(name = "myenv",
                                             file_path = "path-to-conda-specification-file")

# From a pip requirements file
myenv = Environment.from_pip_requirements(name = "myenv"
                                          file_path = "path-to-pip-requirements-file")
```

### <a name="use-existing-conda-environments"></a>使用现有的康达环境

如果本地计算机上存在现有的 Conda 环境，则可以使用该服务创建环境对象。 通过使用此策略，您可以在远程运行时重用本地交互式环境。

以下代码从现有的 Conda 环境`mycondaenv`创建一个环境对象。 它使用[`from_existing_conda_environment()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-existing-conda-environment-name--conda-environment-name-)方法。

``` python
myenv = Environment.from_existing_conda_environment(name = "myenv",
                                                    conda_environment_name = "mycondaenv")
```

### <a name="create-environments-automatically"></a>自动创建环境

通过提交培训运行自动创建环境。 使用`submit()`方法提交运行。 提交培训运行时，新环境的构建可能需要几分钟时间。 生成持续时间取决于所需依赖项的大小。 

如果在提交运行之前未在运行配置中指定环境，则会为您创建默认环境。

```python
from azureml.core import ScriptRunConfig, Experiment, Environment
# Create experiment 
myexp = Experiment(workspace=ws, name = "environment-example")

# Attach training script and compute target to run config
runconfig = ScriptRunConfig(source_directory=".", script="example.py")
runconfig.run_config.target = "local"

# Submit the run
run = myexp.submit(config=runconfig)

# Show each step of run 
run.wait_for_completion(show_output=True)
```

同样，如果将[`Estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py)对象用于训练，则可以直接提交估计器实例作为运行，而无需指定环境。 对象`Estimator`已封装环境和计算目标。

## <a name="add-packages-to-an-environment"></a>将包添加到环境

使用 Conda、pip 或专用滚轮文件将包添加到环境中。 使用[`CondaDependency`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py)类指定每个包依赖项。 将其添加到环境 。 `PythonSection`

### <a name="conda-and-pip-packages"></a>Conda 和 pip 包

如果 Conda 包存储库中有可用的包，我们建议您使用 Conda 安装而不是 pip 安装。 Conda 软件包通常附带预构建的二进制文件，使安装更可靠。

下面的示例增加了环境。 它添加了 版本 1.17.0。 `numpy` 它还添加了`pillow`包。 `myenv` 该示例分别使用[`add_conda_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-conda-package-conda-package-)方法和方法[`add_pip_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-pip-package-pip-package-)。

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Installs numpy version 1.17.0 conda package
conda_dep.add_conda_package("numpy==1.17.0")

# Installs pillow package
conda_dep.add_pip_package("pillow")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep
```

>[!IMPORTANT]
> 如果对另一次运行使用相同的环境定义，Azure 机器学习服务将重用环境的缓存映像。 例如```numpy```，如果创建具有未固定包依赖项的环境，则该环境将继续使用_创建环境时安装的_包版本。 此外，任何具有匹配定义的将来环境都将继续使用旧版本。 有关详细信息，请参阅[环境构建、缓存和重用](https://docs.microsoft.com/azure/machine-learning/concept-environments#environment-building-caching-and-reuse)。

### <a name="private-wheel-files"></a>专用 wheel 文件

您可以通过首先将它们上载到工作区存储来使用专用 pip 轮文件。 使用静态[`add_private_pip_wheel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#add-private-pip-wheel-workspace--file-path--exist-ok-false-)方法上载它们。 然后捕获存储 URL 并将 URL 传递给`add_pip_package()`方法。

```python
# During environment creation the service replaces the URL by secure SAS URL, so your wheel file is kept private and secure
whl_url = Environment.add_private_pip_wheel(workspace=ws,file_path = "my-custom.whl")
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()
conda_dep.add_pip_package(whl_url)
myenv.python.conda_dependencies=conda_dep
```

## <a name="manage-environments"></a>管理环境

管理环境，以便跨计算目标和工作区的其他用户更新、跟踪和重用它们。

### <a name="register-environments"></a>注册环境

在提交运行或部署 Web 服务时，环境将自动注册到工作区。 您还可以使用[`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#register-workspace-)方法手动注册环境。 此操作将环境变成在云中跟踪和个版本的实体。 实体可以在工作区用户之间共享。

以下代码将`myenv`环境注册到`ws`工作区。

```python
myenv.register(workspace=ws)
```

首次在训练或部署中使用环境时，该环境已注册到工作区。 然后，它将在计算目标上生成并部署。 服务缓存环境。 与使用新服务或已更新的服务相比，重复使用已缓存的环境可以节约很多时间。

### <a name="get-existing-environments"></a>获取现有环境

该`Environment`类提供了允许您检索工作区中现有环境的方法。 您可以按名称、列表或特定培训运行检索环境。 此信息有助于故障排除、审核和重现性。

#### <a name="view-a-list-of-environments"></a>查看环境列表

使用[`Environment.list(workspace="workspace_name")`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#list-workspace-)类查看工作区中的环境。 然后选择要重用的环境。

#### <a name="get-an-environment-by-name"></a>按名称获取环境

还可以按名称和版本获取特定环境。 以下代码使用 方法[`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#get-workspace--name--version-none-)检索`1``myenv``ws`工作区上环境的版本。

```python
restored_environment = Environment.get(workspace=ws,name="myenv",version="1")
```

#### <a name="train-a-run-specific-environment"></a>训练特定于运行的环境

要获取在训练完成后用于特定运行的环境，请使用 类中[`get_environment()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-environment--)`Run`的方法。

```python
from azureml.core import Run
Run.get_environment()
```

### <a name="update-an-existing-environment"></a>更新现有环境

假设您通过添加 Python 包来更改现有环境。 然后，在提交运行、部署模型或手动注册环境时，将创建环境的新版本。 版本控制允许您查看环境随时间的变化。

要在现有环境中更新 Python 包版本，请指定该包的版本号。 如果不使用确切的版本号，则 Azure 机器学习将重用现有环境及其原始包版本。

### <a name="debug-the-image-build"></a>调试映像生成

下面的示例使用 方法[`build()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#build-workspace--image-build-compute-none-)手动创建环境作为 Docker 映像。 它使用[`wait_for_completion()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-)监视映像生成中的输出日志。 然后，生成的映像将显示在工作区的 Azure 容器注册表实例中。 此信息有助于调试。

```python
from azureml.core import Image
build = env.build(workspace=ws)
build.wait_for_completion(show_output=True)
```

## <a name="enable-docker"></a>启用 Docker

 Azure[`DockerSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py)机器学习`Environment`类允许您精细自定义和控制运行训练的来宾操作系统。

启用 Docker 时，该服务将生成 Docker 映像。 它还创建一个 Python 环境，该环境在 Docker 容器中使用您的规范。 此功能为您的训练运行提供了额外的隔离和重现性。

```python
# Creates the environment inside a Docker container.
myenv.docker.enabled = True
```

默认情况下，新生成的 Docker 映像将显示在与工作区关联的容器注册表中。  存储库名称的格式为 azureml/azureml_\<uuid\>** 名称的唯一标识符 *（uuid*） 部分对应于从环境配置计算的哈希。 此通信允许服务确定给定环境的映像是否存在以供重用。

此外，该服务会自动使用基于 Ubuntu Linux[的基本映像](https://github.com/Azure/AzureML-Containers)之一。 它安装指定的 Python 包。 基本映像具有 CPU 版本和 GPU 版本。 Azure 机器学习会自动检测要使用的版本。

```python
# Specify custom Docker base image and registry, if you don't want to use the defaults
myenv.docker.base_image="your_base-image"
myenv.docker.base_image_registry="your_registry_location"
```

您还可以指定自定义 Dockerfile。 最简单的方法是使用 Docker```FROM```命令从 Azure 机器学习基本映像之一开始，然后添加自己的自定义步骤。 如果需要将非 Python 包作为依赖项，请使用此方法。

```python
# Specify docker steps as a string. Alternatively, load the string from a file.
dockerfile = r"""
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN echo "Hello from custom container!"
"""

# Set base image to None, because the image is defined by dockerfile.
myenv.docker.base_image = None
myenv.docker.base_dockerfile = dockerfile
```

### <a name="use-user-managed-dependencies"></a>使用用户管理的依赖项

在某些情况下，自定义基本映像可能已包含包含要使用的包的 Python 环境。

默认情况下，Azure 机器学习服务将构建具有您指定的依赖项的 Conda 环境，并将在该环境中执行运行，而不是使用在基本映像上安装的任何 Python 库。 

要使用自己的已安装包，请设置`Environment.python.user_managed_dependencies = True`参数 。 确保基本映像包含 Python 解释器，并具有训练脚本所需的包。

例如，要在安装了 NumPy 包的基本 Miniconda 环境中运行，请先指定具有安装包步骤的 Dockerfile。 然后，将用户托管的依赖项设置为`True`。 

还可以通过设置变量来指定图像中特定 Python 解释器的`Environment.python.interpreter_path`路径。

```python
dockerfile = """
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN conda install numpy
"""

myenv.docker.base_image = None
myenv.docker.base_dockerfile = dockerfile
myenv.python.user_managed_dependencies=True
myenv.python.interpreter_path = "/opt/miniconda/bin/python"
```

## <a name="use-environments-for-training"></a>使用环境进行培训

要提交训练运行，您需要将环境、[计算目标](concept-compute-target.md)和培训 Python 脚本合并到运行配置中。 此配置是用于提交运行的包装对象。

提交培训运行时，构建新环境可能需要几分钟时间。 持续时间取决于所需依赖项的大小。 环境由服务缓存。 因此，只要环境定义保持不变，您只产生一次完整设置时间。

以下本地脚本运行示例显示用作[`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py)包装对象的位置。

```python
from azureml.core import ScriptRunConfig, Experiment
from azureml.core.environment import Environment

exp = Experiment(name="myexp", workspace = ws)
# Instantiate environment
myenv = Environment(name="myenv")

# Add training script to run config
runconfig = ScriptRunConfig(source_directory=".", script="train.py")

# Attach compute target to run config
runconfig.run_config.target = "local"

# Attach environment to run config
runconfig.run_config.environment = myenv

# Submit run 
run = exp.submit(runconfig)
```

> [!NOTE]
> 要禁用运行历史记录或运行快照，请使用 下`ScriptRunConfig.run_config.history`设置。

如果不在运行配置中指定环境，则服务会在提交运行时创建默认环境。

### <a name="use-an-estimator-for-training"></a>使用估计器进行培训

如果您使用[估计器](how-to-train-ml-models.md)进行培训，则可以直接提交估计器实例。 它已经封装了环境和计算目标。

以下代码使用估计器进行单节点训练运行。 它在`scikit-learn`模型的远程计算上运行。 它假定您以前创建了计算目标对象`compute_target`和数据存储对象 。 `ds`

```python
from azureml.train.estimator import Estimator

script_params = {
    '--data-folder': ds.as_mount(),
    '--regularization': 0.8
}

sk_est = Estimator(source_directory='./my-sklearn-proj',
                   script_params=script_params,
                   compute_target=compute_target,
                   entry_script='train.py',
                   conda_packages=['scikit-learn'])

# Submit the run 
run = experiment.submit(sk_est)
```

## <a name="use-environments-for-web-service-deployment"></a>将环境用于 Web 服务部署

在将模型部署为 Web 服务时，可以使用环境。 此功能支持可重现的互联工作流。 在此工作流中，您可以在训练计算和推理计算中使用相同的库来训练、测试和部署模型。

要部署 Web 服务，请将环境、推理计算、评分脚本和已注册的模型合并到部署对象[`deploy()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)中。 有关详细信息，请参阅[部署模型的方式和位置](how-to-deploy-and-where.md)。

在此示例中，假设您已完成培训运行。 现在，您希望将该模型部署到 Azure 容器实例。 生成 Web 服务时，模型和评分文件将安装在映像上，Azure 机器学习推理堆栈将添加到映像中。

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import AciWebservice, Webservice

# Register the model to deploy
model = run.register_model(model_name = "mymodel", model_path = "outputs/model.pkl")

# Combine scoring script & environment in Inference configuration
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

# Set deployment configuration
deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)

# Define the model, inference, & deployment configuration and web service name and location to deploy
service = Model.deploy(
    workspace = ws,
    name = "my_web_service",
    models = [model],
    inference_config = inference_config,
    deployment_config = deployment_config)
```

## <a name="example-notebooks"></a>示例笔记本

此[示例笔记本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/using-environments)扩展了本文中演示的概念和方法。

[使用自定义 Docker 基本映像部署模型](how-to-deploy-custom-docker-image.md)演示如何使用自定义 Docker 基本映像部署模型。

[此示例笔记本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/spark)演示如何将 Spark 模型部署为 Web 服务。

## <a name="create-and-manage-environments-with-the-cli"></a>使用 CLI 创建和管理环境

[Azure 机器学习 CLI](reference-azure-machine-learning-cli.md)反映了 Python SDK 的大部分功能。 您可以使用它创建和管理环境。 本节中讨论的命令演示了基本功能。

以下命令将指定目录中默认环境定义的文件架上支架。 这些文件是JSON文件。 它们的工作方式类似于 SDK 中的相应类。 您可以使用这些文件创建具有自定义设置的新环境。 

```azurecli-interactive
az ml environment scaffold -n myenv -d myenvdir
```

运行以下命令以从指定的目录注册环境。

```azurecli-interactive
az ml environment register -d myenvdir
```

运行以下命令以列出所有已注册的环境。

```azurecli-interactive
az ml environment list
```

使用以下命令下载已注册的环境。

```azurecli-interactive
az ml environment download -n myenv -d downloaddir
```

## <a name="next-steps"></a>后续步骤

* 要使用托管计算目标来训练模型，请参阅[教程：训练模型](tutorial-train-models-with-aml.md)。
* 获得经过训练的模型后，[了解如何部署模型以及部署模型的位置](how-to-deploy-and-where.md)。
* 查看[`Environment`类 SDK 引用](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py)。
* 有关本文中描述的概念和方法的详细信息，请参阅[示例笔记本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/using-environments)。
