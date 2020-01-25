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
ms.openlocfilehash: 9d6c823b5dc85676b15188c8f1783e50e5ed441b
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76717812"
---
# <a name="reuse-environments-for-training-and-deployment-by-using-azure-machine-learning"></a>使用 Azure 机器学习使用培训和部署的环境
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文介绍如何创建和管理 Azure 机器学习[环境](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)。 使用环境来跟踪和重现项目的软件依赖关系。

软件依赖关系管理是开发人员的常见任务。 你需要确保无需大量手动软件配置即可重现生成。 Azure 机器学习 `Environment` 类帐户用于本地开发解决方案，如 pip 和 Conda，它为本地和分布式云开发提供了一个解决方案。

本文中的示例演示如何执行以下操作：

* 创建环境并指定包依赖关系。
* 检索和更新环境。
* 使用环境进行定型。
* 为 web 服务部署使用环境。

若要大致了解环境在 Azure 机器学习中的工作原理，请参阅[什么是 ML 环境？](concept-environments.md)。

## <a name="prerequisites"></a>필수 조건

* [用于 Python 的 AZURE 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* [Azure 机器学习工作区](how-to-manage-workspace.md)

## <a name="create-an-environment"></a>환경 만들기

以下部分将介绍可为试验创建环境的多种方式。

### <a name="use-a-curated-environment"></a>使用特选环境

你可以选择一个特选环境开始： 

* _AzureML-最小_环境包含一组最小的程序包，用于启用运行跟踪和资产上传。 可以将其用作自己的环境的起点。

* _AzureML 教程_环境包含常见的数据科学包。 这些包包括 Scikit-learn、Pandas、Matplotlib 和更大的一组 azureml sdk 包。

特选环境由缓存的 Docker 映像支持。 这种支持降低了运行准备成本。

使用 `Environment.get` 方法选择特选环境之一：

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
>  不要启动自己的环境名称和_AzureML_前缀。 此前缀是为特选环境保留的。

### <a name="instantiate-an-environment-object"></a>实例化环境对象

若要手动创建环境，请从 SDK 导入 `Environment` 类。 然后使用以下代码实例化环境对象。

```python
from azureml.core.environment import Environment
Environment(name="myenv")
```

### <a name="use-conda-and-pip-specification-files"></a>使用 Conda 和 pip 规范文件

还可以从 Conda 规范或 pip 要求文件创建环境。 使用[`from_conda_specification()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-conda-specification-name--file-path-)方法或[`from_pip_requirements()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-pip-requirements-name--file-path-)方法。 在方法参数中，包含你的环境名称和所需文件的文件路径。

```python
# From a Conda specification file
myenv = Environment.from_conda_specification(name = "myenv",
                                             file_path = "path-to-conda-specification-file")

# From a pip requirements file
myenv = Environment.from_pip_requirements(name = "myenv"
                                          file_path = "path-to-pip-requirements-file")
```

### <a name="use-existing-conda-environments"></a>使用现有的 Conda 环境

如果本地计算机上已有 Conda 环境，则可以使用该服务创建环境对象。 通过使用此策略，你可以在远程运行上重用本地交互式环境。

下面的代码 `mycondaenv`中的现有 Conda 环境创建环境对象。 它使用[`from_existing_conda_environment()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-existing-conda-environment-name--conda-environment-name-)方法。

``` python
myenv = Environment.from_existing_conda_environment(name = "myenv",
                                                    conda_environment_name = "mycondaenv")
```

### <a name="create-environments-automatically"></a>自动创建环境

通过提交定型运行来自动创建环境。 使用 `submit()` 方法提交运行。 提交训练运行时，构建新环境可能需要几分钟时间。 生成持续时间取决于所需依赖项的大小。 

如果在提交运行之前未在运行配置中指定环境，则会为你创建一个默认环境。

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

同样，如果使用[`Estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py)对象进行定型，则可以直接在不指定环境的情况下提交估计器实例。 `Estimator` 对象已经封装了环境和计算目标。

## <a name="add-packages-to-an-environment"></a>将包添加到环境

使用 Conda、pip 或专用滚轮文件将包添加到环境。 使用[`CondaDependency`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py)类指定每个包依赖项。 将其添加到环境的 `PythonSection`。

### <a name="conda-and-pip-packages"></a>Conda 和 pip 包

如果 Conda 包存储库中提供了包，则建议使用 Conda 安装而不是 pip 安装。 Conda 包通常附带预生成的二进制文件，使安装更可靠。

下面的示例将添加到环境中。 它添加了 `scikit-learn`的版本0.21.3。 它还添加 `pillow` 包 `myenv`。 该示例分别使用[`add_conda_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-conda-package-conda-package-)方法和[`add_pip_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-pip-package-pip-package-)方法。

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

您可以通过先将专用 pip 轮文件上传到您的工作区存储区来使用这些文件。 使用静态[`add_private_pip_wheel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#add-private-pip-wheel-workspace--file-path--exist-ok-false-)方法上传它们。 然后捕获存储 URL，并将 URL 传递到 `add_pip_package()` 方法。

```python
# During environment creation the service replaces the URL by secure SAS URL, so your wheel file is kept private and secure
whl_url = Environment.add_private_pip_wheel(workspace=ws,file_path = "my-custom.whl")
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()
conda_dep.add_pip_package(whl_url)
myenv.python.conda_dependencies=conda_dep
```

## <a name="manage-environments"></a>환경 관리

管理环境，使你可以跨计算目标以及工作区的其他用户对其进行更新、跟踪和重复使用。

### <a name="register-environments"></a>注册环境

当你提交运行或部署 web 服务时，环境将自动注册到你的工作区。 你还可以使用[`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#register-workspace-)方法手动注册环境。 此操作使环境成为在云中进行跟踪和版本控制的实体。 实体可以在工作区用户之间共享。

下面的代码将 `myenv` 环境注册到 `ws` 工作区。

```python
myenv.register(workspace=ws)
```

当你在定型或部署中首次使用该环境时，它会注册到工作区。 然后，它将在计算目标上生成和部署。 该服务会缓存环境。 重复使用缓存的环境所需的时间比使用新服务或更新的环境少得多。

### <a name="get-existing-environments"></a>获取现有环境

`Environment` 类提供的方法可用于检索工作区中的现有环境。 您可以按名称、列表或特定的定型运行来检索环境。 此信息对于疑难解答、审核和可再现性很有用。

#### <a name="view-a-list-of-environments"></a>查看环境列表

使用[`Environment.list(workspace="workspace_name")`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#list-workspace-)类查看工作区中的环境。 然后选择要重复使用的环境。

#### <a name="get-an-environment-by-name"></a>按名称获取环境

还可以按名称和版本获取特定环境。 下面的代码使用[`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#get-workspace--name--version-none-)方法在 `ws` 工作区中检索 `myenv` 环境的版本 `1`。

```python
restored_environment = Environment.get(workspace=ws,name="myenv",version="1")
```

#### <a name="train-a-run-specific-environment"></a>训练特定于运行的环境

若要在训练完成后获取用于特定运行的环境，请使用 `Run` 类中的[`get_environment()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-environment--)方法。

```python
from azureml.core import Run
Run.get_environment()
```

### <a name="update-an-existing-environment"></a>更新现有环境

假设你更改了现有环境，例如，通过添加 Python 包。 然后，在提交运行、部署模型或手动注册环境时，将创建新版本的环境。 版本控制允许你查看随时间变化的环境更改。

若要更新现有环境中的 Python 包版本，请指定该包的版本号。 如果不使用确切的版本号，Azure 机器学习将使用现有的环境及其原始包版本。

### <a name="debug-the-image-build"></a>调试映像生成

下面的示例使用[`build()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#build-workspace-)方法，手动将环境创建为 Docker 映像。 它使用[`wait_for_completion()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-)来监视来自映像生成的输出日志。 然后，生成的映像将显示在工作区的 Azure 容器注册表实例中。 此信息对于调试很有帮助。

```python
from azureml.core import Image
build = env.build(workspace=ws)
build.wait_for_completion(show_output=True)
```

## <a name="enable-docker"></a>启用 Docker

 通过 Azure 机器学习 `Environment` 类的[`DockerSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py) ，你可以精确地自定义和控制运行定型的来宾操作系统。

`enable` Docker 时，服务将生成一个 Docker 映像。 它还会创建一个 Python 环境，该环境使用该 Docker 容器中的规范。 此功能为您的培训运行提供额外的隔离和可再现性。

```python
# Creates the environment inside a Docker container.
myenv.docker.enabled = True
```

默认情况下，新生成的 Docker 映像会出现在与工作区关联的容器注册表中。  存储库名称的形式为*azureml/azureml_\<uuid\>* 。 名称的唯一标识符（*uuid*）部分对应于从环境配置计算所得的哈希值。 此函件允许服务确定是否已存在给定环境的映像以供重用。

此外，服务会自动使用一个基于 Ubuntu Linux 的[基础映像](https://github.com/Azure/AzureML-Containers)。 它将安装指定的 Python 包。 基本映像具有 CPU 版本和 GPU 版本。 Azure 机器学习会自动检测要使用的版本。

```python
# Specify custom Docker base image and registry, if you don't want to use the defaults
myenv.docker.base_image="your_base-image"
myenv.docker.base_image_registry="your_registry_location"
# Alternatively, you can specify the contents of dockerfile of your base image
with open("docker_file_of_your_base_image", "r") as f:
    dockerfile_contents_of_your_base_image=f.read()
myenv.docker.base_dockerfile=dockerfile_contents_of_your_base_image 
```

> [!NOTE]
> 如果你在使用自定义 Docker 映像时指定 `environment.python.user_managed_dependencies=False`，则该服务将在映像中构建 Conda 环境。 它将在此环境中执行运行，而不是使用在基础映像上安装的任何 Python 库。 将参数设置为 `True`，以使用自己安装的包。

## <a name="use-environments-for-training"></a>使用环境进行培训

若要提交培训运行，需要将你的环境、[计算目标](concept-compute-target.md)和训练 Python 脚本合并到运行配置中。 此配置是用于提交运行的包装对象。

提交训练运行时，构建新的环境可能需要几分钟时间。 持续时间取决于所需依赖项的大小。 环境由服务缓存。 因此，只要环境定义保持不变，就会产生完整的设置时间。

以下本地脚本运行示例显示了将[`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py)用作包装对象的位置。

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

如果未在运行配置中指定环境，则在提交运行时，服务将创建默认环境。

### <a name="use-an-estimator-for-training"></a>使用估计器进行培训

如果使用[估计器](how-to-train-ml-models.md)进行定型，则可以直接提交估计器实例。 它已经封装了环境和计算目标。

以下代码使用估计器来执行单节点定型。 它在 `scikit-learn` 模型的远程计算上运行。 它假定你之前创建了计算目标对象、`compute_target`和数据存储对象 `ds`。

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

## <a name="use-environments-for-web-service-deployment"></a>使用 web 服务部署的环境

在将模型部署为 web 服务时，可以使用环境。 此功能支持可重现的连接工作流。 在此工作流中，可以通过在定型计算和推理计算中使用相同的库来定型、测试和部署模型。

若要部署 web 服务，请在部署对象中合并环境、推理计算、计分脚本和已注册的模型， [`deploy()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)。 有关详细信息，请参阅[部署模型的方式和位置](how-to-deploy-and-where.md)。

在此示例中，假设已完成训练运行。 现在，你想要将该模型部署到 Azure 容器实例。 生成 web 服务时，模型和评分文件会装载到映像上，并将 Azure 机器学习推理堆栈添加到映像。

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

## <a name="example-notebooks"></a>노트북 예제

此[示例笔记本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/using-environments)扩展了本文中演示的概念和方法。

[使用自定义 docker 基本映像部署模型](how-to-deploy-custom-docker-image.md)演示了如何使用自定义 docker 基本映像部署模型。

此[示例笔记本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/spark)演示如何将 Spark 模型部署为 web 服务。

## <a name="create-and-manage-environments-with-the-cli"></a>用 CLI 创建和管理环境

[AZURE 机器学习 CLI](reference-azure-machine-learning-cli.md)反映了 Python SDK 的大部分功能。 你可以使用它来创建和管理环境。 本节中讨论的命令演示了基本功能。

以下命令为指定目录中的默认环境定义基架文件。 这些文件是 JSON 文件。 它们的工作方式类似于 SDK 中的相应类。 您可以使用这些文件创建具有自定义设置的新环境。 

```azurecli-interactive
az ml environment scaffold -n myenv -d myenvdir
```

运行以下命令，从指定目录注册环境。

```azurecli-interactive
az ml environment register -d myenvdir
```

运行以下命令来列出所有已注册的环境。

```azurecli-interactive
az ml environment list
```

使用以下命令下载已注册的环境。

```azurecli-interactive
az ml environment download -n myenv -d downloaddir
```

## <a name="next-steps"></a>다음 단계

* 若要使用托管计算目标来定型模型，请参阅[教程：训练模型](tutorial-train-models-with-aml.md)。
* 获得训练模型后，请了解[部署模型的方式和位置](how-to-deploy-and-where.md)。
* 查看[`Environment` 类 SDK 参考](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py)。
* 有关本文中所述的概念和方法的详细信息，请参阅[示例笔记本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/using-environments)。
