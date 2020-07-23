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
ms.topic: how-to
ms.date: 07/08/2020
ms.custom: tracking-python
ms.openlocfilehash: f9ddc498fdcfe3d1b6da57e012166066feec933e
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2020
ms.locfileid: "86207016"
---
# <a name="how-to-use-environments-in-azure-machine-learning"></a>如何在 Azure 机器学习中使用环境
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文介绍如何创建和管理 Azure 机器学习[环境](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)。 使用环境可以在项目软件依赖项演化时对其进行跟踪和再现。

软件依赖项管理是开发人员的常见任务。 你希望在无需进行大量手动软件配置的情况下，确保版本可以再现。 Azure 机器学习 `Environment` 类通过 Docker 功能实现本地开发解决方案，如 pip 和 Conda 以及分布式云开发。

本文中的示例演示如何执行以下操作：

* 创建环境并指定包依赖项。
* 检索和更新环境。
* 使用环境进行训练。
* 使用环境进行 Web 服务部署。

有关环境如何在 Azure 机器学习中起作用的综合概述，请参阅[什么是 ML 环境？](concept-environments.md)。

## <a name="prerequisites"></a>先决条件

* [适用于 Python 的 Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* 一个 [Azure 机器学习工作区](how-to-manage-workspace.md)

## <a name="create-an-environment"></a>创建环境

以下部分探讨为试验创建环境的多种方式。

### <a name="use-a-curated-environment"></a>使用特选环境

特选环境包含 Python 包的集合，并在默认情况下在工作区中可用。 这些环境由缓存的 Docker 映像支持，减少了运行准备成本。 你可以从以下项中选择一个常用的特选环境： 

* _AzureML-Minimal_ 环境包含最小的一组包，用于启用运行跟踪和资产上传。 在创建自己的环境时，可以将其作为起点。

* _AzureML 教程_环境包含常见的数据科学包。 这些包包括 Scikit-learn、Pandas、Matplotlib 和更大的一组 azureml sdk 包。

有关特选环境的列表，请参阅[特选环境一文](resource-curated-environments.md)。

使用 `Environment.get` 方法选择特选环境之一：

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
env = Environment.get(workspace=ws, name="AzureML-Minimal")
```

若要修改特选环境，必须复制该环境：

```python
env = Environment.get(workspace=ws, name="AzureML-Tutorial").clone("new_env")
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

若要手动创建环境，请 `Environment` 从 SDK 导入类。 然后使用以下代码实例化环境对象。

```python
from azureml.core.environment import Environment
Environment(name="myenv")
```

如果要定义自己的环境，则必须 `azureml-defaults` 使用版本 >= 1.0.45 作为 pip 依赖项列出。 此包包含将模型作为 Web 服务托管时所需的功能。

### <a name="use-conda-pip-and-docker-files"></a>使用 Conda、pip 和 Docker 文件

可以从 Conda 规范或 pip 要求文件创建环境。 使用 [`from_conda_specification()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-conda-specification-name--file-path-) 方法或 [`from_pip_requirements()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-pip-requirements-name--file-path-) 方法。 在方法参数中，包含你的环境名称和所需文件的文件路径。 还可以使用方法从 Docker 文件创建环境 [`load_from_directory()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#load-from-directory-path-) 。 在方法参数中，包括包含 Docker 文件的源目录的路径。 

```python
# From a Conda specification file
myenv = Environment.from_conda_specification(name = "myenv",
                                             file_path = "path-to-conda-specification-file")

# From a pip requirements file
myenv = Environment.from_pip_requirements(name = "myenv"
                                          file_path = "path-to-pip-requirements-file")
                                          
# From a Docker file
myenv = Environment.load_from_directory(path = "path-to-dockerfile-directory")
```

### <a name="use-existing-environments"></a>使用现有环境

如果本地计算机上已有 Conda 环境，则可以使用该服务创建环境对象。 通过使用此策略，你可以在远程运行上重用本地交互式环境。

下面的代码从现有的 Conda 环境创建环境对象 `mycondaenv` 。 它使用 [`from_existing_conda_environment()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-existing-conda-environment-name--conda-environment-name-) 方法。

``` python
myenv = Environment.from_existing_conda_environment(name = "myenv",
                                                    conda_environment_name = "mycondaenv")
```

使用方法，可以轻松地将环境定义保存到可编辑格式的目录中 [`save_to_directory()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#save-to-directory-path--overwrite-false-) 。 修改后，可以通过从目录加载文件来实例化新的环境。

```python
myenv = Environment.save_to_directory(path = "path-to-destination-directory", overwrite = False)
# modify the environment definition
newenv = Environment.load_from_directory(path = "path-to-source-directory")
```

### <a name="create-environments-automatically"></a>自动创建环境

通过提交定型运行来自动创建环境。 使用方法提交运行 `submit()` 。 提交训练运行时，构建新环境可能需要几分钟时间。 生成持续时间取决于所需依赖项的大小。 

如果在提交运行之前未在运行配置中指定环境，则系统会为你创建一个默认环境。

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

同样，如果使用 [`Estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) 对象进行定型，则可以在不指定环境的情况下直接在运行中提交估计器实例。 `Estimator`对象已经封装了环境和计算目标。

## <a name="add-packages-to-an-environment"></a>将包添加到环境

使用 Conda、pip 或专用 wheel 文件将包添加到环境。 使用类指定每个包依赖项 [`CondaDependency`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) 。 将其添加到环境的 `PythonSection` 。

### <a name="conda-and-pip-packages"></a>Conda 和 pip 包

如果 Conda 包存储库中提供了包，则建议使用 Conda 安装而不是 pip 安装。 Conda 包通常附带预生成的二进制文件，使安装更可靠。

以下示例将包添加到环境。 它添加了 1.17.0 版的 `numpy`。 它还会添加 `pillow` 包 `myenv` 。 该示例分别使用 [`add_conda_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-conda-package-conda-package-) 方法和 [`add_pip_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-pip-package-pip-package-) 方法。

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
> 如果对另一次运行使用相同的环境定义，则 Azure 机器学习服务将重复使用您的环境的缓存映像。 如果创建了一个包含未固定包依赖项（例如 ```numpy```）的环境，该环境将继续使用创建环境时安装的包版本。 此外，将来包含匹配定义的任何环境将继续使用旧版本。 有关详细信息，请参阅[环境构建、缓存和重复使用](https://docs.microsoft.com/azure/machine-learning/concept-environments#environment-building-caching-and-reuse)。

### <a name="private-python-packages"></a>专用 Python 包

若要在不将 Python 包公开到公共 internet 的情况下安全地使用它，请参阅[如何使用专用 Python 包](how-to-use-private-python-packages.md)一文。

## <a name="manage-environments"></a>管理环境

管理环境，使你可以跨计算目标以及工作区的其他用户对其进行更新、跟踪和重复使用。

### <a name="register-environments"></a>注册环境

在提交运行或部署 Web 服务时，环境将自动注册到工作区。 你还可以使用方法手动注册环境 [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#register-workspace-) 。 此操作使环境成为在云中进行跟踪和版本控制的实体。 实体可以在工作区用户之间共享。

下面的代码将 `myenv` 环境注册到 `ws` 工作区。

```python
myenv.register(workspace=ws)
```

当你在定型或部署中首次使用该环境时，它会注册到工作区。 然后，它将在计算目标上生成和部署。 该服务会缓存环境。 与使用新服务或已更新的服务相比，重复使用已缓存的环境可以节约很多时间。

### <a name="get-existing-environments"></a>获取现有环境

`Environment`类提供允许你在工作区中检索现有环境的方法。 您可以按名称、列表或特定的定型运行来检索环境。 此信息对于疑难解答、审核和可再现性很有用。

#### <a name="view-a-list-of-environments"></a>查看环境列表

使用类查看工作区中的环境 [`Environment.list(workspace="workspace_name")`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#list-workspace-) 。 然后选择要重复使用的环境。

#### <a name="get-an-environment-by-name"></a>按名称获取环境

还可以按名称和版本获取特定环境。 下面的代码使用 [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#get-workspace--name--version-none-) 方法在 `1` `myenv` 工作区中检索环境的版本 `ws` 。

```python
restored_environment = Environment.get(workspace=ws,name="myenv",version="1")
```

#### <a name="train-a-run-specific-environment"></a>训练特定于运行的环境

若要在训练完成后获取用于特定运行的环境，请使用 [`get_environment()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-environment--) 类中的方法 `Run` 。

```python
from azureml.core import Run
Run.get_environment()
```

### <a name="update-an-existing-environment"></a>更新现有环境

假设你更改了现有环境，例如，通过添加 Python 包。 这将需要一些时间作为新版本的环境生成，然后在提交运行、部署模型或手动注册环境时创建。 版本控制允许你查看随时间变化的环境更改。 

若要更新现有环境中的 Python 包版本，请指定该包的版本号。 如果不使用确切的版本号，Azure 机器学习将使用现有的环境及其原始包版本。

### <a name="debug-the-image-build"></a>调试映像生成

下面的示例使用 [`build()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#build-workspace--image-build-compute-none-) 方法将环境手动创建为 Docker 映像。 它使用监视来自映像生成的输出日志 [`wait_for_completion()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-) 。 然后，生成的映像将显示在工作区的 Azure 容器注册表实例中。 此信息对于调试很有帮助。

```python
from azureml.core import Image
build = env.build(workspace=ws)
build.wait_for_completion(show_output=True)
```

首先使用方法在本地生成图像非常有用 [`build_local()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#build-local-workspace--platform-none----kwargs-) 。 设置可选参数 `pushImageToWorkspaceAcr = True` 会将生成的映像推送到 AZURE ML 工作区容器注册表中。 

## <a name="enable-docker"></a>启用 Docker

[`DockerSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py)通过 Azure 机器学习类的，你可以精确 `Environment` 地自定义和控制运行定型的来宾操作系统。 `arguments`变量可用于指定要传递到 Docker run 命令的额外参数。

启用 Docker 后，服务将生成一个 Docker 映像。 它还会创建一个 Python 环境，该环境使用该 Docker 容器中的规范。 此功能为您的培训运行提供额外的隔离和可再现性。

```python
# Creates the environment inside a Docker container.
myenv.docker.enabled = True
```

默认情况下，新生成的 Docker 映像会出现在与工作区关联的容器注册表中。  存储库名称的形式为*azureml/azureml_ \<uuid\> *。 名称 (*uuid*) 部分的唯一标识符对应于从环境配置计算的哈希值。 此函件允许服务确定是否已存在给定环境的映像以供重用。

此外，服务会自动使用一个基于 Ubuntu Linux 的[基础映像](https://github.com/Azure/AzureML-Containers)。 它将安装指定的 Python 包。 基本映像具有 CPU 版本和 GPU 版本。 Azure 机器学习会自动检测要使用的版本。 还可以使用[自定义 Docker 基本映像](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-custom-docker-image#create-a-custom-base-image)。

```python
# Specify custom Docker base image and registry, if you don't want to use the defaults
myenv.docker.base_image="your_base-image"
myenv.docker.base_image_registry="your_registry_location"
```

还可以指定自定义 Dockerfile。 最简单的方法是使用 Docker 命令从一个 Azure 机器学习基础映像开始 ```FROM``` ，然后添加自己的自定义步骤。 如果需要将非 Python 包作为依赖项安装，请使用此方法。 请记住将基本映像设置为 "无"。

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

### <a name="use-user-managed-dependencies"></a>使用用户托管的依赖项

在某些情况下，自定义基本映像可能已包含包含要使用的包的 Python 环境。

默认情况下，Azure 机器学习服务将生成具有指定的依赖项的 Conda 环境，并将在该环境中执行运行，而不是使用在基础映像上安装的任何 Python 库。 由于 Conda 环境与自定义基本映像隔离，因此不会包括在其他位置安装的包。

若要使用自己安装的包并禁用 Conda，请设置参数 `Environment.python.user_managed_dependencies = True` 。 确保基本映像包含 Python 解释器，并具有您的培训脚本所需的包。

例如，若要在安装了 NumPy 包的基本 Miniconda) 环境中运行，请首先指定 Dockerfile，其中包含安装包的步骤。 然后将用户管理的依赖项设置为 `True` 。 

还可以通过设置变量来指定图像内特定 Python 解释器的路径 `Environment.python.interpreter_path` 。

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

若要提交训练运行，需要将你的环境、[计算目标](concept-compute-target.md)和训练 Python 脚本组合到运行配置中。 此配置是用于提交运行的包装器对象。

提交训练运行时，构建新环境可能需要几分钟时间。 持续时间取决于所需依赖项的大小。 环境由服务缓存。 因此，只要环境定义保持不变，完整的设置过程就只需执行一次即可。

以下本地脚本运行示例显示了将 [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) 用作包装对象的位置。

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
> 若要禁用运行历史记录或运行快照，请使用中的设置 `ScriptRunConfig.run_config.history` 。

如果未在运行配置中指定环境，则在提交运行时，服务将创建默认环境。

### <a name="use-an-estimator-for-training"></a>使用估计器进行培训

如果使用[估计器](how-to-train-ml-models.md)进行定型，则可以直接提交估计器实例。 它已经封装了环境和计算目标。

以下代码使用估计器来执行单节点定型。 它在模型的远程计算上运行 `scikit-learn` 。 它假定你之前创建了计算目标对象、 `compute_target` 和数据存储对象 `ds` 。

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

在将模型部署为 Web 服务时，可以使用环境。 此功能支持可重现的连接工作流。 在此工作流中，可以通过在定型计算和推理计算中使用相同的库来定型、测试和部署模型。

若要部署 Web 服务，请将环境、推理计算、评分脚本和已注册的模型组合到你的部署对象 [`deploy()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) 中。 有关详细信息，请参阅[部署模型的方式和位置](how-to-deploy-and-where.md)。

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

## <a name="example-notebooks"></a>示例笔记本

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

## <a name="next-steps"></a>后续步骤

* 若要使用托管计算目标来定型模型，请参阅[教程：训练模型](tutorial-train-models-with-aml.md)。
* 获得训练模型后，请了解[部署模型的方式和位置](how-to-deploy-and-where.md)。
* 查看[ `Environment` 类 SDK 参考](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py)。
* 有关本文中所述的概念和方法的详细信息，请参阅[示例笔记本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/using-environments)。
