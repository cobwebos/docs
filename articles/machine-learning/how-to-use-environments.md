---
title: 使用软件环境
titleSuffix: Azure Machine Learning
description: 创建和管理用于模型训练和部署的环境。 管理环境的 Python 包和其他设置。
services: machine-learning
author: saachigopal
ms.author: sagopal
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.date: 07/23/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 4db53b806adc2e29ae9c9a950faf8fc822c9d66b
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91743975"
---
# <a name="create--use-software-environments-in-azure-machine-learning"></a>在 Azure 机器学习中创建和使用软件环境


本文介绍如何创建和管理 Azure 机器学习[环境](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true)。 使用环境可以在项目软件依赖项演化时对其进行跟踪和再现。

软件依赖项管理是开发人员的常见任务。 你希望在无需进行大量手动软件配置的情况下，确保版本可以再现。 Azure 机器学习 `Environment` 类会考虑本地开发解决方案（例如 pip 和 Conda），并通过 Docker 功能进行分布式云开发。

本文中的示例演示如何执行以下操作：

* 创建环境并指定包依赖项。
* 检索和更新环境。
* 使用环境进行训练。
* 使用环境进行 Web 服务部署。

有关环境在 Azure 机器学习中的作用的综合概述，请参阅[什么是 ML 环境？](concept-environments.md) 若要了解如何配置开发环境，请参阅[此文](how-to-configure-environment.md)。

## <a name="prerequisites"></a>先决条件

* [用于 Python 的 AZURE 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true) ( # B0 = 1.13.0) 
* 一个 [Azure 机器学习工作区](how-to-manage-workspace.md)

## <a name="create-an-environment"></a>创建环境

以下部分探讨为试验创建环境的多种方式。

### <a name="instantiate-an-environment-object"></a>实例化环境对象

若要手动创建环境，请从 SDK 导入 `Environment` 类。 然后使用以下代码实例化某个环境对象。

```python
from azureml.core.environment import Environment
Environment(name="myenv")
```

### <a name="use-a-curated-environment"></a>使用特选环境

特选环境包含 Python 包的集合，默认情况下可以在你的工作区中使用。 这些环境由缓存的 Docker 映像支持，降低了运行准备成本。 可以先从这些常用的特选环境中选择一个： 

* _AzureML-Minimal_ 环境包含最小的一组包，用于启用运行跟踪和资产上传。 在创建自己的环境时，可以将其作为起点。

* _AzureML-Tutorial_ 环境包含常用的数据科学包。 这些包包括 Scikit-learn、Pandas 和 Matplotlib，以及更大的 azureml-sdk 包集。

如需特选环境的列表，请参阅[特选环境](resource-curated-environments.md)一文。

使用 `Environment.get` 方法选择一个特选环境：

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
>  请不要在自己的环境名称的开头使用 _AzureML_ 前缀。 此前缀专用于特选环境。

### <a name="use-conda-dependencies-or-pip-requirements-files"></a>使用 Conda 依赖项或 pip 要求文件

可以通过 Conda 规范或 pip 要求文件创建环境。 使用 [`from_conda_specification()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true#&preserve-view=truefrom-conda-specification-name--file-path-) 方法或 [`from_pip_requirements()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true#&preserve-view=truefrom-pip-requirements-name--file-path-) 方法。 在方法参数中包含所需文件的环境名称和文件路径。 

```python
# From a Conda specification file
myenv = Environment.from_conda_specification(name = "myenv",
                                             file_path = "path-to-conda-specification-file")

# From a pip requirements file
myenv = Environment.from_pip_requirements(name = "myenv"
                                          file_path = "path-to-pip-requirements-file")                                          
```

### <a name="enable-docker"></a>启用 Docker

当你启用 Docker 时，Azure 机器学习会生成一个 Docker 映像，并根据你的规范在该容器中创建一个 Python 环境。 Docker 映像会被缓存并重复使用：在新环境中首次运行的时间通常比构建映像的时间更长。

使用 Azure 机器学习 `Environment` 类的 [`DockerSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py&preserve-view=true)，可以精细地自定义和控制运行训练的来宾操作系统。 可以使用 `arguments` 变量来指定要传递到 Docker run 命令的额外参数。

```python
# Creates the environment inside a Docker container.
myenv.docker.enabled = True
```

默认情况下，新生成的 Docker 映像显示在与工作区关联的容器注册表中。  存储库名称的格式为“azureml/azureml_\<uuid\>”。 该名称的唯一标识符 (*uuid*) 部分对应于基于环境配置计算出的哈希。 这种对应使得服务能够确定给定的环境是否已存在可重复使用的映像。

#### <a name="use-a-prebuilt-docker-image"></a>使用预生成的 Docker 映像

默认情况下，服务会自动使用一个基于 Ubuntu Linux 的 [基础映像](https://github.com/Azure/AzureML-Containers)，特别是由定义的一个映像 `azureml.core.environment.DEFAULT_CPU_IMAGE` 。 然后，它将安装由提供的 Azure ML 环境定义的任何指定的 Python 包。 还可以使用[自定义 Docker 基础映像](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-custom-docker-image#create-a-custom-base-image)。

```python
# Specify custom Docker base image and registry, if you don't want to use the defaults
myenv.docker.base_image="your_base-image"
myenv.docker.base_image_registry="your_registry_location"
```

>[!IMPORTANT]
> Azure 机器学习仅支持提供以下软件的 Docker 映像：
> * Ubuntu 16.04 或更高版本。
> * Conda 4.5.# 或更高版本。
> * Python 3.5 +。

#### <a name="use-your-own-dockerfile"></a>使用你自己的 Dockerfile 

还可以指定自定义 Dockerfile。 最简单的方法是使用 Docker ```FROM``` 命令从某个 Azure 机器学习基础映像开始，然后添加自己的自定义步骤。 如果需要安装非 Python 包作为依赖项，请使用此方法。 记住将基础映像设置为“None”。

```python
# Specify docker steps as a string. 
dockerfile = r"""
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN echo "Hello from custom container!"
"""

# Set base image to None, because the image is defined by dockerfile.
myenv.docker.base_image = None
myenv.docker.base_dockerfile = dockerfile

# Alternatively, load the string from a file.
myenv.docker.base_image = None
myenv.docker.base_dockerfile = "./Dockerfile"
```

#### <a name="specify-your-own-python-interpreter"></a>指定你自己的 Python 解释器

在某些情况下，自定义基础映像可能已包含带有你要使用的包的 Python 环境。

若要使用你自己的已安装包并禁用 Conda，请设置参数 `Environment.python.user_managed_dependencies = True`。 请确保基础映像包含 Python 解释器，并包含你的训练脚本所需的包。

例如，若要在安装了 NumPy 包的基本 Miniconda 环境中运行，请首先指定 Dockerfile，其中包含安装包的步骤。 然后将用户管理的依赖项设置为 `True`。 

还可以通过设置 `Environment.python.interpreter_path` 变量来指定特定 Python 解释器在映像中的路径。

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

> [!WARNING]
> 如果在 Docker 映像中安装了某些 Python 依赖项，但忘记设置 `user_managed_dependencies=True` ，则这些包在执行环境中将不存在，从而导致运行时失败。 默认情况下，Azure ML 会使用你指定的依赖项构建一个 Conda 环境，并会在该环境中执行运行，而不是使用你在基础映像上安装的任何 Python 库。

#### <a name="retrieve-image-details"></a>检索映像详细信息

对于已注册的环境，可以使用以下代码检索映像详细信息，其中 `details` 是 [DockerImageDetails](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockerimagedetails?view=azure-ml-py&preserve-view=true) 的实例 (AzureML Python SDK >= 1.11)，并提供有关环境映像的所有信息，例如 dockerfile、注册表和映像名称。

```python
details = environment.get_image_details(workspace=ws)
```

若要从执行运行的环境中获取映像详细信息，请使用以下代码：

```python
details = run.get_environment().get_image_details(workspace=ws)
```

### <a name="use-existing-environments"></a>使用现有环境

如果本地计算机上存在现有的 Conda 环境，则可以使用服务创建环境对象。 采用这种策略可以在远程运行中重复使用本地交互式环境。

以下代码从现有的 Conda 环境 `mycondaenv` 创建环境对象。 它使用 [`from_existing_conda_environment()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true#&preserve-view=truefrom-existing-conda-environment-name--conda-environment-name-) 方法。

``` python
myenv = Environment.from_existing_conda_environment(name="myenv",
                                                    conda_environment_name="mycondaenv")
```

环境定义可以通过 [`save_to_directory()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true#&preserve-view=truesave-to-directory-path--overwrite-false-) 方法以易于编辑的格式保存到目录。 进行修改后，可以通过从目录加载文件来实例化新的环境。

```python
myenv = Environment.save_to_directory(path="path-to-destination-directory", overwrite=False)
# modify the environment definition
newenv = Environment.load_from_directory(path="path-to-source-directory")
```

### <a name="implicitly-use-the-default-environment"></a>隐式使用默认环境

如果在提交运行之前未在脚本运行配置中指定环境，则会为你创建一个默认环境。

```python
from azureml.core import ScriptRunConfig, Experiment, Environment
# Create experiment 
myexp = Experiment(workspace=ws, name = "environment-example")

# Attach training script and compute target to run config
src = ScriptRunConfig(source_directory=".", script="example.py", compute_target="local")

# Submit the run
run = myexp.submit(config=src)

# Show each step of run 
run.wait_for_completion(show_output=True)
```

## <a name="add-packages-to-an-environment"></a>将包添加到环境

使用 Conda、pip 或专用 wheel 文件将包添加到环境。 使用 [`CondaDependency`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py&preserve-view=true) 类指定每个包依赖项。 将此依赖项添加到环境的 `PythonSection`。

### <a name="conda-and-pip-packages"></a>Conda 和 pip 包

如果某个包已在 Conda 包存储库中提供，则相较于 pip 安装，我们更建议使用 Conda 安装。 Conda 包通常附带预生成的二进制文件，能提高安装的可靠性。

以下示例将其添加到环境 `myenv`。 它添加了 1.17.0 版的 `numpy`。 它还添加 `pillow` 包。 该示例分别使用 [`add_conda_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py&preserve-view=true#&preserve-view=trueadd-conda-package-conda-package-) 方法和 [`add_pip_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py&preserve-view=true#&preserve-view=trueadd-pip-package-pip-package-) 方法。

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

还可以将环境变量添加到环境。 然后，可以在训练脚本中使用 os.environ.get 让它们变得可用。

```python
myenv.environment_variables = {"MESSAGE":"Hello from Azure Machine Learning"}
```

>[!IMPORTANT]
> 如果你对另一个运行使用相同的环境定义，Azure 机器学习服务将重复使用环境的已缓存映像。 如果创建了一个包含未固定包依赖项（例如 ```numpy```）的环境，该环境将继续使用创建环境时安装的包版本。 此外，将来包含匹配定义的任何环境将继续使用旧版本。 有关详细信息，请参阅[生成、缓存和重复使用环境](https://docs.microsoft.com/azure/machine-learning/concept-environments#environment-building-caching-and-reuse)。

### <a name="private-python-packages"></a>专用 Python 包

若要在不将 Python 包公开到公共 Internet 的情况下以专用且安全的方式使用它们，请参阅[如何使用专用 Python 包](how-to-use-private-python-packages.md)一文。

## <a name="manage-environments"></a>管理环境

管理环境，以便可以跨计算目标以及与其他工作区用户共同更新、跟踪和重复使用这些环境。

### <a name="register-environments"></a>注册环境

在提交运行或部署 Web 服务时，环境将自动注册到工作区。 还可以使用 [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=trueregister-workspace-) 方法手动注册环境。 此操作可让环境成为云中受到跟踪且经过版本控制的实体。 可以在工作区用户之间共享该实体。

以下代码将 `myenv` 环境注册到 `ws` 工作区。

```python
myenv.register(workspace=ws)
```

在训练或部署中首次使用环境时，该环境将注册到工作区。 然后，它将在计算目标上生成并部署。 服务将缓存环境。 与使用新服务或已更新的服务相比，重复使用已缓存的环境可以节约很多时间。

### <a name="get-existing-environments"></a>获取现有环境

`Environment` 类提供一些方法用于检索工作区中的现有环境。 可按名称、以列表形式或者按特定的训练运行检索环境。 此信息有助于故障排除、审核和再现。

#### <a name="view-a-list-of-environments"></a>查看环境列表

使用 [`Environment.list(workspace="workspace_name")`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=truelist-workspace-) 类查看工作区中的环境。 然后选择要重复使用的环境。

#### <a name="get-an-environment-by-name"></a>按名称获取环境

还可以按名称和版本获取特定环境。 以下代码使用 [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=trueget-workspace--name--version-none-) 方法检索 `ws` 工作区中 `myenv` 环境的版本 `1`。

```python
restored_environment = Environment.get(workspace=ws,name="myenv",version="1")
```

#### <a name="train-a-run-specific-environment"></a>训练特定于运行的环境

若要在训练完成后获取用于特定运行的环境，请使用 `Run` 类中的 [`get_environment()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py&preserve-view=true#&preserve-view=trueget-environment--) 方法。

```python
from azureml.core import Run
Run.get_environment()
```

### <a name="update-an-existing-environment"></a>更新现有环境

假设你更改了现有环境，例如，通过添加 Python 包。 这需要时间来构建，因为在你提交运行、部署模型或手动注册环境时，系统会创建该环境的新版本。 版本控制让你可以查看环境在不同时间的变化。 

若要更新现有环境中的 Python 包版本，请指定该包的版本号。 如果不使用确切的版本号，Azure 机器学习会重复使用现有环境及其原始包版本。

### <a name="debug-the-image-build"></a>调试映像生成

以下示例使用 [`build()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=truebuild-workspace--image-build-compute-none-) 方法将环境手动创建为 Docker 映像。 它使用 [`wait_for_completion()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=truewait-for-creation-show-output-false-) 监视映像生成中的输出日志。 然后，生成的映像将显示在工作区的 Azure 容器注册表实例中。 此信息有助于调试。

```python
from azureml.core import Image
build = env.build(workspace=ws)
build.wait_for_completion(show_output=True)
```

可以首先使用 [`build_local()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true#&preserve-view=truebuild-local-workspace--platform-none----kwargs-) 方法在本地生成映像。 若要生成 docker 映像，请设置可选参数 `useDocker=True`。 若要将生成的映像推送到 AzureML 工作区容器注册表，请设置 `pushImageToWorkspaceAcr=True`。

```python
build = env.build_local(workspace=ws, useDocker=True, pushImageToWorkspaceAcr=True)
```

> [!WARNING]
>  更改环境中的依赖项或通道的顺序将产生新的环境，并将需要新的映像生成。 此外，如果存在新版本，则为现有映像调用 `build()` 方法将更新其依赖项。 

## <a name="use-environments-for-training"></a>使用环境进行训练

若要提交训练运行，需要将你的环境、[计算目标](concept-compute-target.md)和训练 Python 脚本组合到运行配置中。 此配置是用于提交运行的包装器对象。

提交训练运行时，生成新环境可能需要几分钟时间。 具体的持续时间取决于所需依赖项的大小。 环境由服务缓存。 因此，只要环境定义保持不变，完整安装就只会发生一次。

以下本地脚本运行示例显示应使用 [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py&preserve-view=true) 作为包装器对象的地方。

```python
from azureml.core import ScriptRunConfig, Experiment
from azureml.core.environment import Environment

exp = Experiment(name="myexp", workspace = ws)
# Instantiate environment
myenv = Environment(name="myenv")

# Configure the ScriptRunConfig and specify the environment
src = ScriptRunConfig(source_directory=".", script="train.py", target="local", environment=myenv)

# Submit run 
run = exp.submit(src)
```

> [!NOTE]
> 若要禁用运行历史记录或运行快照，请使用 `src.run_config.history` 下的设置。

如果未在运行配置中指定环境，服务将在提交运行时创建一个默认环境。

## <a name="use-environments-for-web-service-deployment"></a>使用环境进行 Web 服务部署

在将模型部署为 Web 服务时，可以使用环境。 此功能可以实现可再现的联网工作流。 在此工作流中，可在训练计算和推理计算中使用相同的库来训练、测试和部署模型。


如果你要为 Web 服务部署定义自己的环境，则必须将版本不低于 1.0.45 的 `azureml-defaults` 列出为 pip 依赖项。 此包包含将模型作为 Web 服务托管时所需的功能。

若要部署 Web 服务，请将环境、推理计算、评分脚本和已注册的模型组合到部署对象 [`deploy()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) 中。 有关详细信息，请参阅[部署模型的方式和位置](how-to-deploy-and-where.md)。

此示例假设你已完成训练运行。 现在你希望将模型部署到 Azure 容器实例。 生成 Web 服务时，模型和评分文件会装载到映像上，并且 Azure 机器学习推理堆栈会添加到映像中。

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

## <a name="notebooks"></a>笔记本

此[文章](https://docs.microsoft.com/azure/machine-learning/how-to-run-jupyter-notebooks#add-new-kernels)介绍如何将 Conda 环境安装为笔记本中的内核。

[使用自定义 Docker 基础映像部署模型](how-to-deploy-custom-docker-image.md)演示了如何使用自定义的 Docker 基础映像部署模型。

[此示例笔记本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/spark)演示了如何将 Spark 模型部署为 Web 服务。

## <a name="create-and-manage-environments-with-the-cli"></a>使用 CLI 创建和管理环境

[Azure 机器学习 CLI](reference-azure-machine-learning-cli.md) 具备 Python SDK 的大部分功能。 可以使用它来创建和管理环境。 本节中讨论的命令演示了基本功能。

以下命令为指定目录中的默认环境定义创建基架文件。 这些文件是 JSON 文件。 其工作方式类似于 SDK 中的相应类。 可以使用这些文件创建采用自定义设置的新环境。 

```azurecli-interactive
az ml environment scaffold -n myenv -d myenvdir
```

运行以下命令可从指定的目录注册环境。

```azurecli-interactive
az ml environment register -d myenvdir
```

运行以下命令可以列出所有已注册的环境。

```azurecli-interactive
az ml environment list
```

使用以下命令下载已注册的环境。

```azurecli-interactive
az ml environment download -n myenv -d downloaddir
```

## <a name="next-steps"></a>后续步骤

* 若要使用托管的计算目标来训练模型，请参阅[教程：训练模型](tutorial-train-models-with-aml.md)。
* 训练模型后，了解[部署模型的方式和位置](how-to-deploy-and-where.md)。
* 查看 [`Environment` 类 SDK 参考](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py&preserve-view=true)。
