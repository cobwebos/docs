---
title: 创建可重用的 ML 环境
titleSuffix: Azure Machine Learning
description: 创建和管理模型定型和部署环境。 管理适用于环境的 Python 包和其他设置。
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: af6848e85db5d2a557835b063a499e3439557eb6
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/07/2020
ms.locfileid: "75690433"
---
# <a name="reuse-environments-for-training--deployment-with-azure-machine-learning"></a>重复使用 Azure 机器学习的培训 & 部署环境。
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，将了解如何创建和管理 Azure 机器学习[环境](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)，以便您可以跟踪和重现项目的软件依赖关系。

软件依赖关系管理是开发人员的常见任务。 您希望能够确保无需手动软件配置即可重现生成。 使用用于本地开发的解决方案（如 pip 和 Conda），Azure 机器学习环境类为本地和分布式云开发提供了一个解决方案。

本文中的示例演示如何执行以下操作：

* 创建环境并指定包依赖关系
* 检索和更新环境
* 使用环境进行培训
* 使用 web 服务部署的环境

有关环境在 Azure 机器学习中的工作原理的详细概述，请参阅[概念文章](concept-environments.md)。

## <a name="prerequisites"></a>必备组件

* [安装](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)的适用于 Python 的 Azure 机器学习 SDK。
* [Azure 机器学习工作区](how-to-manage-workspace.md)。

## <a name="create-an-environment"></a>创建环境

有多种方法可为试验创建环境。

### <a name="use-curated-environment"></a>使用特选环境

你可以选择一个特选环境以开始。 

* __AzureML-最小__环境包含一组最小的程序包，用于启用运行跟踪和资产上传。 可以将其用作自己的环境的起点。

* __Azureml 教程__环境包含常见的数据科学包，如 Scikit-learn、Pandas 和 Matplotlib，以及更大的 AzureML sdk 包集。

特选环境由缓存的 Docker 映像支持，从而降低了运行准备成本。

使用__环境. get__方法可选择其中一个特选环境：

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
env = Environment.get(workspace=ws, name="AzureML-Minimal")
```

可以使用以下代码列出特选环境及其包：
```python
envs = Environment.list(workspace=ws)

for env in envs:
    if env.startswith("AzureML"):
        print("Name",env)
        print("packages", envs[env].python.conda_dependencies.serialize_to_string())
```

> [!WARNING]
>  不要启动自己的环境名称，并提供_AzureML_前缀。 它是为特选环境保留的。

### <a name="instantiate-an-environment-object"></a>实例化环境对象

若要手动创建环境，请从 SDK 导入环境类，然后使用以下代码实例化环境对象。

```python
from azureml.core.environment import Environment
Environment(name="myenv")
```

### <a name="conda-and-pip-specification-files"></a>Conda 和 pip 规范文件

还可以从 Conda 规范或 pip 要求文件创建环境。
使用[from_conda_specification （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-conda-specification-name--file-path-)或[from_pip_requirements （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-pip-requirements-name--file-path-)方法，并在方法参数中包含所需文件的环境名称和文件路径。

```python
# From a Conda specification file
myenv = Environment.from_conda_specification(name = "myenv",
                                             file_path = "path-to-conda-specification-file")

#From a pip requirements file
myenv = Environment.from_pip_requirements(name = "myenv"
                                          file_path = "path-to-pip-requirements-file")
```

### <a name="existing-conda-environment"></a>现有 Conda 环境

如果本地计算机上有现有的 Conda 环境，则该服务将提供用于创建环境对象的解决方案。 这样，你就可以在远程运行上重用本地交互式环境。

以下代码使用[from_existing_conda_environment （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-existing-conda-environment-name--conda-environment-name-)方法 `mycondaenv` 从现有的 Conda 环境中创建环境对象。

``` python
myenv = Environment.from_existing_conda_environment(name = "myenv",
                                                    conda_environment_name = "mycondaenv")
```

### <a name="automatically-create-environments"></a>自动创建环境

通过使用 submit （）方法提交定型运行来自动创建环境。 提交定型运行时，构建新环境可能需要几分钟时间，具体取决于所需依赖项的大小。 

如果在提交运行之前未在运行配置中指定环境，则会为你创建一个默认环境。

```python
from azureml.core import ScriptRunConfig, Experiment, Environment
# Create experiment 
myexp = Experiment(workspace=ws, name = "environment-example")

# Attaches training script and compute target to run config
runconfig = ScriptRunConfig(source_directory=".", script="example.py")
runconfig.run_config.target = "local"

# Submit the run
run = myexp.submit(config=runconfig)

# Shows each step of run 
run.wait_for_completion(show_output=True)
```

同样，如果使用[`Estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py)对象进行定型，则可以直接提交估计器实例，而无需指定环境。 `Estimator` 对象已经封装了环境和计算目标。


## <a name="add-packages-to-an-environment"></a>将包添加到环境

将包添加到包含 Conda、pip 或专用滚轮文件的环境。 使用[CondaDependency 类](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py)指定每个包依赖项，并将其添加到环境的 PythonSection 中。

### <a name="conda-and-pip-packages"></a>Conda 和 pip 包

如果包在 Conda 包存储库中可用，则建议使用 Conda over pip 安装。 原因在于，Conda 包通常附带预生成的二进制文件，使安装更可靠。

下面的示例将 `scikit-learn`（特别是版本0.21.3）和 `pillow` 包添加到环境中，分别 `myenv` [`add_conda_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-conda-package-conda-package-)和[`add_pip_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-pip-package-pip-package-)方法。

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Installs scikit-learn version 0.21.3 conda package
conda_dep.add_conda_package("scikit-learn==0.21.3")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep
```

### <a name="private-wheel-files"></a>专用轮文件

通过使用静态[`add_private_pip_wheel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#add-private-pip-wheel-workspace--file-path--exist-ok-false-)方法，然后捕获存储 URL，并将 URL 传递到 `add_pip_package()` 方法，你可以使用专用 pip 轮文件，方法是先将该文件上传到你的工作区存储。

```python
# During environment creation the service replaces the URL by secure SAS URL, so your wheel file is kept private and secure
whl_url = Environment.add_private_pip_wheel(workspace=ws,file_path = "my-custom.whl")
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()
conda_dep.add_pip_package(whl_url)
myenv.python.conda_dependencies=conda_dep
```

## <a name="manage-environments"></a>管理环境

管理环境，以便您可以跨计算目标以及工作区的其他用户对其进行更新、跟踪和重复使用。

### <a name="register-environments"></a>注册环境

当你提交运行或部署 web 服务时，环境将自动注册到你的工作区。 你还可以使用[register （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#register-workspace-)方法手动注册环境。 此操作使环境成为在云中进行跟踪和版本控制的实体，并且可以在工作区用户之间共享。

下面的代码向工作区注册环境 `myenv``ws`。

```python
myenv.register(workspace=ws)
```

当首次使用培训或部署时，环境将注册到在计算目标上生成和部署的工作区。 环境由服务缓存。 重复使用缓存的环境所需的时间比使用新服务或更新的环境少得多。

### <a name="get-existing-environments"></a>获取现有环境

环境类提供一些方法，通过这些方法，你可以按名称检索工作区中的现有环境，作为列表，或者通过特定的培训运行进行故障排除或审核（可再现性）。

#### <a name="view-list-of-environments"></a>查看环境列表

使用[`Environment.list(workspace="workspace_name")`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#list-workspace-)查看工作区中的环境，然后选择一个用于重用。

#### <a name="get-environment-by-name"></a>按名称获取环境

还可以按名称和版本获取特定环境。
下面的代码使用[get （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#get-workspace--name--version-none-)方法检索环境 `1` 版本，`myenv` 在 `ws` 工作区中。

```python
restored_environment = Environment.get(workspace=ws,name="myenv",version="1")
```

#### <a name="training-run-specific-environment"></a>培训运行特定环境

若要在训练完成后获取用于特定运行的环境，请使用 Run 类中的[get_environment （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-environment--)方法。

```python
from azureml.core import Run
Run.get_environment()
```

### <a name="update-an-existing-environment"></a>更新现有环境

如果对现有环境进行更改（如添加 Python 包），则在提交运行、部署模型或手动注册环境时，将创建新版本的环境。 版本控制允许你查看随时间变化的环境更改。

若要更新现有环境的 Python 包版本，请指定该包的准确版本号。 否则，在创建环境时，Azure 机器学习将重复使用现有环境中的包版本。

### <a name="debug-the-image-build"></a>调试映像生成

此示例使用[build （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#build-workspace-)方法以 Docker 映像的形式手动创建环境，并使用[wait_for_completion （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-)监视映像生成的输出日志。 然后，生成的映像将显示在 Azure 容器注册表的工作区中，这对于调试很有用。

```python
from azureml.core import Image
build = env.build(workspace=ws)
build.wait_for_completion(show_output=True)
```

## <a name="docker-and-environments"></a>Docker 和环境

 利用 Azure 机器学习 `Environments` 类的[DockerSection](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py) ，你可以自定义和控制在其中执行定型运行的来宾操作系统。

当你 `enable` Docker 时，服务将生成一个 Docker 映像，并根据你的规范在该 Docker 容器中创建一个 Python 环境。 这为定型运行提供了额外的隔离和可再现性。

```python
# Creates the environment inside a Docker container.
myenv.docker.enabled = True
```

构建后，Docker 映像会出现在与工作区关联的 Azure 容器注册表中（默认情况下）。  存储库名称的形式为*azureml/azureml_\<uuid\>* 。 唯一标识符（*uuid*）部分对应于从环境配置计算的哈希。 这允许服务确定是否已存在与给定环境相对应的映像，以便重复使用。

此外，服务会自动使用一个基于 Ubuntu Linux 的[基础映像](https://github.com/Azure/AzureML-Containers)，并安装指定的 Python 包。 基本映像具有 CPU 和 GPU 版本。 Azure 机器学习会自动检测要使用的版本。

```python
# Specify custom Docker base image and registry, if you don't want to use the defaults
myenv.docker.base_image="your_base-image"
myenv.docker.base_image_registry="your_registry_location"
```

> [!NOTE]
> 如果你在使用自定义 Docker 映像时指定 `environment.python.user_managed_dependencies=False`，则该服务将在该映像中构建 Conda 环境，并在该环境中执行运行，而不是使用可能已安装在基本映像上的 Python 库。 将参数设置为 `True`，以使用自己安装的包。

## <a name="using-environments-for-training"></a>使用环境进行培训

若要提交培训运行，需要将你的环境、[计算目标](concept-compute-target.md)和培训 Python 脚本合并到运行配置中;用于提交运行的包装对象。

提交定型运行时，构建新的环境可能需要几分钟时间，具体取决于所需依赖项的大小。 环境由服务缓存，因此只要环境定义保持不变，则完整的设置时间只发生一次。

以下本地脚本运行示例显示了使用[ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py)作为包装对象的位置。

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
> 若要禁用运行历史记录或运行快照，请使用 `ScriptRunConfig.run_config.history`下的设置。

如果未在运行配置中指定环境，则在提交运行时，服务将为你创建一个默认环境。

### <a name="train-with-an-estimator"></a>使用估算器进行训练

如果使用[估计器](how-to-train-ml-models.md)进行定型，只需直接提交估计器实例，因为它已经封装了环境和计算目标。

以下代码使用估计器在远程计算上针对 scikit-learn 模型运行单节点定型，并假设先前创建的计算目标对象 `compute_target` 和数据存储对象 `ds`。

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

## <a name="using-environments-for-web-service-deployment"></a>使用用于 web 服务部署的环境

在将模型部署为 web 服务时，可以使用环境。 这可以实现可重复连接的工作流，在此工作流中，你可以在定型和推理计算中使用完全相同的库定型、测试和部署模型。

若要部署 web 服务，请在部署对象[（）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)中合并环境、推理计算、计分脚本和已注册的模型。 了解有关[部署 web 服务](how-to-deploy-and-where.md)的详细信息。

在此示例中，假设已完成训练运行，并且想要将该模型部署到 Azure 容器实例（ACI）中。 生成 web 服务时，模型和评分文件会装载到映像上，并将 Azure 机器学习推理堆栈添加到映像。

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import AciWebservice, Webservice

# Register the model to deploy
model = run.register_model(model_name = "mymodel", model_path = "outputs/model.pkl")

# Combine scoring script & environment in Inference configuration
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

# Set deployment configuration
deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)

# Define the model, inference & deployment configuration and web service name and location to deploy
service = Model.deploy(
    workspace = ws,
    name = "my_web_service",
    models = [model],
    inference_config = inference_config,
    deployment_config = deployment_config)
```

## <a name="example-notebooks"></a>示例笔记本

此[示例笔记本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/using-environments)扩展了本文中演示的概念和方法。

## <a name="next-steps"></a>后续步骤

* [教程：定型模型](tutorial-train-models-with-aml.md)使用托管计算目标来定型模型。
* 训练模型后，了解[如何以及在何处部署模型](how-to-deploy-and-where.md)。
* 查看[环境类](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py)SDK 参考。
