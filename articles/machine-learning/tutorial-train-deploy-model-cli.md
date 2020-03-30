---
title: 通过 CLI 训练和部署模型
titleSuffix: Azure Machine Learning
description: 了解如何使用适用于 Azure CLI 的机器学习扩展通过命令行训练、注册和部署模型。
ms.author: larryfr
author: Blackmist
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: 401ce2aed2c783169592f0dc664a3a7baea415b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336627"
---
# <a name="tutorial-train-and-deploy-a-model-from-the-cli"></a>教程：从 CLI 训练和部署模型
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本教程中，你将使用适用于 Azure CLI 的机器学习扩展来训练、注册和部署模型。

本教程中的 Python 训练脚本使用 [scikit-learn](https://scikit-learn.org/) 来训练一个基本模型。 本教程介绍的重点不是脚本或模型，而是通过 CLI 使用 Azure 机器学习的过程。

了解如何执行以下操作：

> [!div class="checklist"]
> * 安装机器学习扩展
> * 创建 Azure 机器学习工作区
> * 创建用于训练模型的计算资源
> * 定义和注册用于训练模型的数据集
> * 启动训练运行
> * 注册和下载模型
> * 将模型部署为 Web 服务
> * 使用 Web 服务为数据评分

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请在开始操作前先创建一个免费帐户。 立即试用[免费版或付费版 Azure 机器学习](https://aka.ms/AMLFree)。

* 若要在**本地环境**中使用本文档所述的 CLI 命令，需要安装 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

    如果使用 Azure[云外壳](https://azure.microsoft.com//features/cloud-shell/)，则 CLI 将通过浏览器访问并生活在云中。

## <a name="download-the-example-project"></a>下载示例项目

对于本教程，下载项目[https://github.com/microsoft/MLOps](https://github.com/microsoft/MLOps)。 本教程中的步骤使用 `examples/cli-train-deploy` 目录中的文件。

若要获取这些文件的本地副本，请[下载 .zip 存档](https://github.com/microsoft/MLOps/archive/master.zip)，或使用以下 Git 命令克隆存储库：

```azurecli-interactive
git clone https://github.com/microsoft/MLOps.git
```

### <a name="training-files"></a>训练文件

项目中的 `examples/cli-train-deploy` 目录包含以下在训练模型时要使用的文件：

* `.azureml\mnist.runconfig`：__运行配置文件__。 此文件定义训练模型所需的运行时环境。 在此示例中，它还会将用于训练模型的数据装入训练环境。
* `scripts\train.py`：训练脚本。 此文件训练模型。
* `scripts\utils.py`：训练脚本使用的帮助程序文件。
* `.azureml\conda_dependencies.yml`：定义运行训练脚本所需的软件依赖项。
* `dataset.json`：数据集定义。 用于在 Azure 机器学习工作区中注册 MNIST 数据集。

### <a name="deployment-files"></a>部署文件

存储库包含以下文件，在将训练的模型部署为 Web 服务时要使用这些文件：

* `aciDeploymentConfig.yml`：__部署配置文件__。 此文件定义模型所需的托管环境。
* `inferenceConfig.json`：__推理配置文件__。 此文件定义软件环境，服务使用该环境通过模型为数据评分。
* `score.py`：接受传入数据的 python 脚本，使用模型对它进行评分，然后返回响应。
* `scoring-env.yml`：运行模型和`score.py`脚本所需的 conda 依赖项。
* `testdata.json`：可用于测试已部署的 Web 服务的数据文件。

## <a name="connect-to-your-azure-subscription"></a>连接到 Azure 订阅

可通过多种方法从 CLI 对 Azure 订阅进行身份验证。 最基本的方法是使用浏览器进行交互式身份验证。 若要以交互方式进行身份验证，请打开命令行或终端，然后使用以下命令：

```azurecli-interactive
az login
```

如果 CLI 可以打开默认的浏览器，则它会打开该浏览器并加载登录页。 否则，需要打开浏览器并按照命令行中的说明操作。 这些说明涉及浏览[https://aka.ms/devicelogin](https://aka.ms/devicelogin)和输入授权代码。

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

## <a name="install-the-machine-learning-extension"></a>安装机器学习扩展

若要安装机器学习扩展，请使用以下命令：

```azurecli-interactive
az extension add -n azure-cli-ml
```

如果有消息指出该扩展已安装，请使用以下命令更新到最新版本：

```azurecli-interactive
az extension update -n azure-cli-ml
```

## <a name="create-a-resource-group"></a>创建资源组

资源组是 Azure 平台上的资源的基本容器。 使用 Azure 机器学习时，资源组将包含 Azure 机器学习工作区。 它还将包含工作区使用的其他 Azure 服务。 例如，如果使用基于云的计算资源来训练模型，则会在资源组中创建该资源。

若要创建新资源组，请使用以下命令。____ 请将 `<resource-group-name>` 替换为此资源组使用的名称。 请将 `<location>` 替换为此资源组使用的 Azure 区域：

> [!TIP]
> 应选择可在其中使用 Azure 机器学习的区域。 有关信息，请参阅[各区域的产品可用性](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service)。

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

此命令返回的响应类似于以下 JSON：

```json
{
  "id": "/subscriptions/<subscription-GUID>/resourceGroups/<resourcegroupname>",
  "location": "<location>",
  "managedBy": null,
  "name": "<resource-group-name>",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

有关使用资源组的详细信息，请参阅 [az group](https://docs.microsoft.com//cli/azure/group?view=azure-cli-latest)。

## <a name="create-a-workspace"></a>创建工作区

若要创建新工作区，请使用以下命令。 请将 `<workspace-name>` 替换为此工作区使用的名称。 请将 `<resource-group-name>` 替换为资源组的名称：

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

此命令的输出类似于以下 JSON：

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

## <a name="connect-local-project-to-workspace"></a>将本地项目连接到工作区

在终端中或命令提示符下，使用以下命令将目录切换到 `cli-train-deploy` 目录，然后连接到工作区：

```azurecli-interactive
cd ~/MLOps/examples/cli-train-deploy
az ml folder attach -w <workspace-name> -g <resource-group-name>
```

此命令的输出类似于以下 JSON：

```json
{
  "Experiment name": "model-training",
  "Project path": "/home/user/MLOps/examples/cli-train-deploy",
  "Resource group": "<resource-group-name>",
  "Subscription id": "<subscription-id>",
  "Workspace name": "<workspace-name>"
}
```

此命令创建一个 `.azureml/config.json` 文件，其中包含连接到工作区所需的信息。 本教程中使用的剩余 `az ml` 命令将使用此文件，因此无需将工作区和资源组添加到所有命令。

## <a name="create-the-compute-target-for-training"></a>创建用于训练的计算目标

此示例使用 Azure 机器学习计算群集来训练模型。 若要创建新的计算群集，请使用以下命令：

```azurecli-interactive
az ml computetarget create amlcompute -n cpu-cluster --max-nodes 4 --vm-size Standard_D2_V2
```

此命令的输出类似于以下 JSON：

```json
{
  "location": "<location>",
  "name": "cpu-cluster",
  "provisioningErrors": null,
  "provisioningState": "Succeeded"
}
```

此命令创建名为 `cpu-cluster` 的新计算目标，其中最多包含四个节点。 所选的 VM 大小为 VM 提供 GPU 资源。 有关 VM 大小的信息，请参阅 [VM 类型和大小]。

> [!IMPORTANT]
> 计算目标的名称（在本例中为 `cpu-cluster`）非常重要；在下一部分中使用的 `.azureml/mnist.runconfig` 文件将引用此名称。

## <a name="define-the-dataset"></a>定义数据集

若要训练模型，可以使用数据集提供训练数据。 若要通过 CLI 创建数据集，必须提供数据集定义文件。 存储库中提供的 `dataset.json` 文件使用 MNIST 数据创建新的数据集。 创建的数据集名为 `mnist-dataset`。

若要使用 `dataset.json` 文件注册数据集，请使用以下命令：

```azurecli-interactive
az ml dataset register -f dataset.json --skip-validation
```

此命令的输出类似于以下 JSON：

```json
{
  "definition": [
    "GetFiles"
  ],
  "registration": {
    "description": "mnist dataset",
    "id": "a13a4034-02d1-40bd-8107-b5d591a464b7",
    "name": "mnist-dataset",
    "tags": {
      "sample-tag": "mnist"
    },
    "version": 1,
    "workspace": "Workspace.create(name='myworkspace', subscription_id='mysubscriptionid', resource_group='myresourcegroup')"
  },
  "source": [
    "http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz",
    "http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz",
    "http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz",
    "http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz"
  ]
}
```

> [!IMPORTANT]
> 复制 `id` 条目的值，以便在下一部分使用。

要查看数据集的更全面的模板，请使用以下命令：

```azurecli-interactive
az ml dataset register --show-template
```

## <a name="reference-the-dataset"></a>引用数据集

要使数据集可在训练环境使用，必须从 runconfig 文件引用它。 `.azureml/mnist.runconfig` 文件包含以下 YAML 条目：

```yaml
# The arguments to the script file.
arguments:
- --data-folder
- DatasetConsumptionConfig:mnist

.....

# The configuration details for data.
data:
  mnist:
# Data Location
    dataLocation:
# the Dataset used for this run.
      dataset:
# Id of the dataset.
        id: a13a4034-02d1-40bd-8107-b5d591a464b7
# the DataPath used for this run.
      datapath:
# Whether to create new folder.
    createOutputDirectories: false
# The mode to handle
    mechanism: mount
# Point where the data is download or mount or upload.
    environmentVariableName: mnist
# relative path where the data is download or mount or upload.
    pathOnCompute:
# Whether to overwrite the data if existing.
    overwrite: false
```

更改 `id` 条目的值，使其与注册数据集时返回的值相匹配。 此值用于在训练期间将数据载入计算目标。

此 YAML 在培训期间会导致以下操作：

* 在训练环境中装载数据集（基于数据集的 ID），并将路径存储在环境变量中`mnist`装载点的路径。
* 使用 `--data-folder` 参数将训练环境中的数据（装入点）位置传递到脚本。

runconfig 文件还包含用于配置训练运行所用环境的信息。 检查此文件时将会看到，它引用了前面创建的 `cpu-compute` 计算目标。 它还列出了训练时要使用的节点数 (`"nodeCount": "4"`)，并包含一个 `"condaDependencies"` 节，该节列出了运行训练脚本所需的 Python 包。

> [!TIP]
> 可以手动创建 runconfig 文件，不过，此示例中的配置文件是使用存储库中包含的 `generate-runconfig.py` 文件创建的。 此文件将获取对已注册数据集的引用，以编程方式创建运行配置，然后将其保存到文件中。

有关运行配置文件的详细信息，请参阅[为模型培训设置和使用计算目标](how-to-set-up-training-targets.md#create-run-configuration-and-submit-run-using-azure-machine-learning-cli)。 有关完整的 JSON 引用，请参阅[runconfigschema.json](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json)。

## <a name="submit-the-training-run"></a>提交训练运行

若要在 `cpu-cluster` 计算目标上启动训练运行，请使用以下命令：

```azurecli-interactive
az ml run submit-script -c mnist -e myexperiment --source-directory scripts -t runoutput.json
```

此命令指定试验的名称 (`myexperiment`)。 试验将有关此运行的信息存储在工作区中。

`-c mnist` 参数指定 `.azureml/mnist.runconfig` 文件。

`-t` 参数将对此运行的引用存储在 JSON 文件中，并在后续步骤中用于注册和下载模型。

在执行训练运行过程中，它会在远程计算资源上流式传输训练会话中的信息。 部分信息类似于以下文本：

```output
Predict the test set
Accuracy is 0.9185
```

此文本是从训练脚本记录的，显示模型的准确度。 其他模型具有不同的性能指标。

检查训练脚本时将会注意到，在将训练的模型存储到 `outputs/sklearn_mnist_model.pkl` 时，该脚本还会使用 alpha 值。

模型已保存到训练该模型的计算目标上的 `./outputs` 目录中。 在本例中，计算目标是 Azure 云中的 Azure 机器学习计算实例。 训练过程自动将 `./outputs` 目录的内容从发生训练的计算目标上传到 Azure 机器学习工作区。 此内容存储为试验（在本示例中为 `myexperiment`）的一部分。

## <a name="register-the-model"></a>注册模型

若要直接从试验中存储的版本注册模型，请使用以下命令：

```azurecli-interactive
az ml model register -n mymodel -f runoutput.json --asset-path "outputs/sklearn_mnist_model.pkl" -t registeredmodel.json
```

此命令将训练运行创建的 `outputs/sklearn_mnist_model.pkl` 文件注册为名为 `mymodel` 的新模型注册。 `--assets-path` 引用试验中的路径。 在本例中，试验和运行信息是从训练命令创建的 `runoutput.json` 文件中加载的。 `-t registeredmodel.json` 创建一个 JSON 文件，该文件引用此命令新建的已注册模型，并由其他处理已注册模型的 CLI 命令使用。

此命令的输出类似于以下 JSON：

```json
{
  "createdTime": "2019-09-19T15:25:32.411572+00:00",
  "description": "",
  "experimentName": "myexperiment",
  "framework": "Custom",
  "frameworkVersion": null,
  "id": "mymodel:1",
  "name": "mymodel",
  "properties": "",
  "runId": "myexperiment_1568906070_5874522d",
  "tags": "",
  "version": 1
}
```

### <a name="model-versioning"></a>模型版本控制

记下返回的模型版本号。 每次使用此名称注册新模型时，版本都会递增。 例如，可以下载模型，然后使用以下命令从本地文件注册该模型：

```azurecli-interactive
az ml model download -i "mymodel:1" -t .
az ml model register -n mymodel -p "sklearn_mnist_model.pkl"
```

第一个命令将已注册的模型下载到当前目录。 文件名为 `sklearn_mnist_model.pkl`，这是注册模型时引用的文件。 第二个命令使用与上述注册相同的名称 (`mymodel`) 注册本地模型 (`-p "sklearn_mnist_model.pkl"`)。 这一次，返回的 JSON 数据列出了版本 2。

## <a name="deploy-the-model"></a>部署模型

若要部署模型，请使用以下命令：

```azurecli-interactive
az ml model deploy -n myservice -m "mymodel:1" --ic inferenceConfig.json --dc aciDeploymentConfig.yml
```

> [!NOTE]
> 您可能会收到有关"无法检查本地 Web 服务存在"或"无法创建 Docker 客户端"的警告。 您可以安全地忽略这一点，因为您没有部署本地 Web 服务。

此命令使用前面注册的模型版本 1 部署名为 `myservice` 的新服务。

该文件`inferenceConfig.yml`提供有关如何使用模型进行推理的信息。 例如，它引用条目脚本 （`score.py`） 和软件依赖项。

有关此文件的结构的详细信息，请参阅[推理配置架构](reference-azure-machine-learning-cli.md#inference-configuration-schema)。 有关入口脚本的详细信息，请参阅[使用 Azure 机器学习部署模型](how-to-deploy-and-where.md#prepare-to-deploy)。

`aciDeploymentConfig.yml` 描述用于托管服务的部署环境。 部署配置特定于用于部署的计算类型。 本例中使用了 Azure 容器实例。 有关详细信息，请参阅[部署配置架构](reference-azure-machine-learning-cli.md#deployment-configuration-schema)。

需要花费几分钟时间来完成部署过程。

> [!TIP]
> 本示例使用了 Azure 容器实例。 到 ACI 的部署会自动创建所需的 ACI 资源。 如果改为部署到 Azure Kubernetes 服务，必须提前创建 AKS 群集，并将其指定为 `az ml model deploy` 命令的一部分。 有关部署到 AKS 的示例，请参阅[将模型部署到 Azure Kubernetes 服务群集](how-to-deploy-azure-kubernetes-service.md)。

几分钟后，将返回类似于以下 JSON 的信息：

```json
ACI service creation operation finished, operation "Succeeded"
{
  "computeType": "ACI",
  {...ommitted for space...}
  "runtimeType": null,
  "scoringUri": "http://6c061467-4e44-4f05-9db5-9f9a22ef7a5d.eastus2.azurecontainer.io/score",
  "state": "Healthy",
  "tags": "",
  "updatedAt": "2019-09-19T18:22:32.227401+00:00"
}
```

### <a name="the-scoring-uri"></a>评分 URI

从部署返回的 `scoringUri` 是部署为 Web 服务的模型的 REST 终结点。 也可以使用以下命令获取此 URI：

```azurecli-interactive
az ml service show -n myservice
```

此命令返回相同的 JSON 文档，其中包括 `scoringUri`。

REST 终结点可用于将数据发送到服务。 有关创建可将数据发送到服务的客户端应用程序的信息，请参阅[使用部署为 Web 服务的 Azure 机器学习模型](how-to-consume-web-service.md)

### <a name="send-data-to-the-service"></a>将数据发送到服务

可以创建一个客户端应用程序来调用终结点，同时，机器学习 CLI 提供了一个可充当测试客户端的实用工具。 使用以下命令将 `testdata.json` 文件中的数据发送到服务：

```azurecli-interactive
az ml service run -n myservice -d @testdata.json
```

> [!TIP]
> 如果使用 PowerShell，请使用以下命令：
>
> ```azurecli-interactive
> az ml service run -n myservice -d `@testdata.json
> ```

该命令的响应类似于 `[ 3 ]`。

## <a name="clean-up-resources"></a>清理资源

> [!IMPORTANT]
> 已创建的资源可以用作其他 Azure 机器学习教程和操作方法文章的先决条件。

### <a name="delete-deployed-service"></a>删除已部署的服务

如果你打算继续使用 Azure 机器学习工作区，但想要删除已部署的服务以降低成本，请使用以下命令：

```azurecli-interactive
az ml service delete -n myservice
```

此命令返回一个 JSON 文档，其中包含已删除的服务的名称。 删除服务可能需要几分钟时间。

### <a name="delete-the-training-compute"></a>删除训练计算

如果你打算继续使用 Azure 机器学习工作区，但想要删除为训练创建的 `cpu-cluster` 计算目标，请使用以下命令：

```azurecli-interactive
az ml computetarget delete -n cpu-cluster
```

此命令返回一个 JSON 文档，其中包含已删除的计算目标的 ID。 删除计算目标可能需要几分钟时间。

### <a name="delete-everything"></a>删除所有内容

如果你不打算使用创建的资源，请将其删除，以免产生额外的费用。

若要删除资源组以及在本文档中创建的所有 Azure 资源，请使用以下命令。 请将 `<resource-group-name>` 替换为前面创建的资源组的名称：

```azurecli-interactive
az group delete -g <resource-group-name> -y
```

## <a name="next-steps"></a>后续步骤

在本 Azure 机器学习教程中，你已使用机器学习 CLI 完成以下任务：

> [!div class="checklist"]
> * 安装机器学习扩展
> * 创建 Azure 机器学习工作区
> * 创建用于训练模型的计算资源
> * 定义和注册用于训练模型的数据集
> * 启动训练运行
> * 注册和下载模型
> * 将模型部署为 Web 服务
> * 使用 Web 服务为数据评分

有关使用 CLI 的详细信息，请参阅[使用适用于 Azure 机器学习的 CLI 扩展](reference-azure-machine-learning-cli.md)。
