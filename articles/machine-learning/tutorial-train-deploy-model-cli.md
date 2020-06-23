---
title: 通过 CLI 训练和部署模型
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure CLI 的机器学习扩展通过命令行训练、注册和部署模型。
ms.author: larryfr
author: Blackmist
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 03/26/2020
ms.openlocfilehash: f3603bf8afdcd990144897113f4e8506629f60a3
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2020
ms.locfileid: "84429751"
---
# <a name="tutorial-train-and-deploy-a-model-from-the-cli"></a>教程：通过 CLI 训练和部署模型
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本教程中，你将使用 Azure CLI 的机器学习扩展来训练、注册和部署模型。

本教程中的 Python 训练脚本使用 [scikit-learn](https://scikit-learn.org/) 来训练基本模型。 本教程的侧重点不是脚本或模型，而是使用 CLI 处理 Azure 机器学习的过程。

了解如何执行以下操作：

> [!div class="checklist"]
> * 安装机器学习扩展
> * 创建 Azure 机器学习工作区
> * 创建用于训练模型的计算资源
> * 定义并注册用于训练模型的数据集
> * 开始训练运行
> * 注册和下载模型
> * 将模型部署为 Web 服务
> * 使用 Web 服务对数据评分

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请在开始操作前先创建一个免费帐户。 立即试用[免费版或付费版 Azure 机器学习](https://aka.ms/AMLFree)。

* 若要从本地环境使用本文档中的 CLI 命令，需要使用 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

    如果使用的是 [Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/)，则可通过浏览器访问 CLI（它位于云端）。

## <a name="download-the-example-project"></a>下载示例项目

对于本教程，请下载 [https://github.com/microsoft/MLOps](https://github.com/microsoft/MLOps) 项目。 本教程中的步骤使用 `examples/cli-train-deploy` 目录中的文件。

若要获取文件的本地副本，请[下载 .zip 存档](https://github.com/microsoft/MLOps/archive/master.zip)，或使用以下 Git 命令克隆存储库：

```azurecli-interactive
git clone https://github.com/microsoft/MLOps.git
```

### <a name="training-files"></a>训练文件

项目中的 `examples/cli-train-deploy` 目录包含以下文件，这些文件在训练模型时使用：

* `.azureml\mnist.runconfig`：“运行配置”文件。 此文件定义训练模型所需的运行时环境。 在本例中，它还将用于训练模型的数据装载到训练环境中。
* `scripts\train.py`：训练脚本。 此文件用于训练模型。
* `scripts\utils.py`：训练脚本使用的帮助程序文件。
* `.azureml\conda_dependencies.yml`：定义运行训练脚本所需的软件依赖项。
* `dataset.json`：数据集定义。 用于在 Azure 机器学习工作区中注册 MNIST 数据集。

### <a name="deployment-files"></a>部署文件

存储库包含以下文件，这些文件用于将训练的模型部署为 Web 服务：

* `aciDeploymentConfig.yml`：“部署配置”文件。 此文件定义模型所需的承载环境。
* `inferenceConfig.json`：“推理配置”文件。 该文件定义了使用模型对数据进行评分的服务所用的软件环境。
* `score.py`：一个 python 脚本，它用于接受传入数据、使用模型对该数据评分，然后返回响应。
* `scoring-env.yml`：运行模型和 `score.py` 脚本所需的 conda 依赖项。
* `testdata.json`：可用于测试已部署的 Web 服务的数据文件。

## <a name="connect-to-your-azure-subscription"></a>连接到 Azure 订阅

可使用多种方法通过 CLI 对 Azure 订阅进行身份验证。 最基本的方法是使用浏览器进行交互式身份验证。 若要以交互方式进行身份验证，请打开命令行或终端，然后使用以下命令：

```azurecli-interactive
az login
```

如果 CLI 可以打开默认的浏览器，则它会打开该浏览器并加载登录页。 否则，需要打开浏览器并按照命令行中的说明操作。 按照说明操作时，需要浏览到 [https://aka.ms/devicelogin](https://aka.ms/devicelogin) 并输入授权代码。

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

## <a name="install-the-machine-learning-extension"></a>安装机器学习扩展

若要安装机器学习扩展，请使用以下命令：

```azurecli-interactive
az extension add -n azure-cli-ml
```

如果收到“扩展已安装”消息，请使用以下命令更新到最新版本：

```azurecli-interactive
az extension update -n azure-cli-ml
```

## <a name="create-a-resource-group"></a>创建资源组

资源组是 Azure 平台上资源的基本容器。 使用 Azure 机器学习时，资源组将包含 Azure 机器学习工作区。 它还将包含工作区使用的其他 Azure 服务。 例如，如果使用基于云的计算资源训练模型，则该资源将在资源组中进行创建。

若要创建新的资源组，请使用以下命令。 将 `<resource-group-name>` 替换为此资源组使用的名称。 将 `<location>` 替换为此资源组使用的 Azure 区域：

> [!TIP]
> 应选择可使用 Azure 机器学习的区域。 有关详细信息，请参阅 [各区域的产品可用性](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service)。

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

此命令的响应类似于以下 JSON：

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

要详细了解如何使用资源组，请参阅 [az group](https://docs.microsoft.com//cli/azure/group?view=azure-cli-latest)。

## <a name="create-a-workspace"></a>创建工作区

若要创建新的工作区，请使用以下命令。 将 `<workspace-name>` 替换为要用于此工作区的名称。 `<resource-group-name>` 替换为资源组的名称：

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

在终端或命令提示符下，使用以下命令将目录更改为 `cli-train-deploy` 目录，然后连接到工作区：

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

此命令会创建一个 `.azureml/config.json` 文件，其中包含连接到工作区所需的信息。 本教程中使用的 `az ml` 命令的其余部分将使用此文件，因此你无需将工作区和资源组添加到所有命令中。

## <a name="create-the-compute-target-for-training"></a>创建用于训练的计算目标

本例使用 Azure 机器学习计算群集来训练模型。 若要创建新的计算群集，请使用以下命令：

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

此命令会新建一个名为 `cpu-cluster` 的计算目标，其中最多包含 4 个节点。 所选的虚拟机 (VM) 大小为 VM 提供了 GPU 资源。 要了解 VM 大小，请参阅 [VM 类型和大小]。

> [!IMPORTANT]
> 计算目标的名称（在本例中为 `cpu-cluster`）很重要；在下一部分中使用的 `.azureml/mnist.runconfig` 文件会引用它。

## <a name="define-the-dataset"></a>定义数据集

要训练模型，可使用数据集提供训练数据。 要通过 CLI 创建数据集，必须提供数据集定义文件。 存储库中提供的 `dataset.json` 文件使用 MNIST 数据创建新的数据集。 它创建的数据集命名为 `mnist-dataset`。

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
> 复制 `id` 项的值，下一部分将用到它。

若要查看数据集的更全面模板，请使用以下命令：

```azurecli-interactive
az ml dataset register --show-template
```

## <a name="reference-the-dataset"></a>引用数据集

若要使数据集在训练环境中可用，必须从 runconfig 文件中引用它。 `.azureml/mnist.runconfig` 文件包含以下 YAML 项：

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

更改 `id` 项的值，使其与注册数据集时返回的值匹配。 此值用于在训练期间将数据加载到计算目标中。

此 YAML 在训练过程中将导致以下操作：

* 在训练环境中装载数据集（基于数据集的 ID），并将装入点的路径存储在 `mnist` 环境变量中。
* 使用 `--data-folder` 参数将训练环境中的数据位置（装入点）传递给脚本。

runconfig 文件还包含用于配置训练运行所使用的环境的信息。 如果检查此文件，将看到它引用了之前创建的 `cpu-compute` 计算目标。 它还列出了训练时要使用的节点数 (`"nodeCount": "4"`)，并包含一个 `"condaDependencies"` 部分，其中列出了运行训练脚本所需的 Python 包。

> [!TIP]
> 虽然可手动创建 runconfig 文件，但本例中的文件是使用存储库中包含的 `generate-runconfig.py` 文件创建的。 此文件获取对已注册的数据集的引用，以编程方式创建运行配置，然后将其保存到文件中。

要详细了解运行配置文件，请参阅[设置计算目标并将其用于模型训练](how-to-set-up-training-targets.md#create-run-configuration-and-submit-run-using-azure-machine-learning-cli)。 有关完整的 JSON 引用，请参阅 [runconfigschema.json](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json)。

## <a name="submit-the-training-run"></a>提交训练运行

若要在 `cpu-cluster` 计算目标上开始训练运行，请使用以下命令：

```azurecli-interactive
az ml run submit-script -c mnist -e myexperiment --source-directory scripts -t runoutput.json
```

此命令会指定试验的名称 (`myexperiment`)。 实验将此运行的相关信息存储在工作区中。

`-c mnist` 参数指定 `.azureml/mnist.runconfig` 文件。

`-t` 参数在 JSON 文件中存储对该运行的引用，并将在后续步骤中用于注册和下载模型。

在训练运行过程中，它将流式传输来自远程计算资源上的训练会话的信息。 部分信息类似于以下文本：

```output
Predict the test set
Accuracy is 0.9185
```

此文本从训练脚本中记录，显示了模型的准确性。 其他模型将具有不同的性能指标。

如果检查训练脚本，你会注意到它在将训练模型存储到 `outputs/sklearn_mnist_model.pkl` 时也使用 Alpha 值。

模型已保存到计算目标上训练它的 `./outputs` 目录中， 在本例中为 Azure 云中的 Azure 机器学习计算实例。 训练过程会将 `./outputs` 目录的内容从进行训练的计算目标自动上传到 Azure 机器学习工作区。 它存储为试验的一部分（在本例中为 `myexperiment`）。

## <a name="register-the-model"></a>注册模型

若要直接从试验中存储的版本注册模型，请使用以下命令：

```azurecli-interactive
az ml model register -n mymodel -f runoutput.json --asset-path "outputs/sklearn_mnist_model.pkl" -t registeredmodel.json
```

此命令将训练运行创建的 `outputs/sklearn_mnist_model.pkl` 文件注册为名为 `mymodel` 的新模型注册。 `--assets-path` 会引用试验中的路径。 在本例中，试验和运行信息是从训练命令创建的 `runoutput.json` 文件中加载的。 `-t registeredmodel.json` 会创建一个 JSON 文件，该文件引用由该命令创建的新注册模型，并由处理注册模型的其他 CLI 命令使用。

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

记下为模型返回的版本号。 每次使用此名称注册新模型时，版本都会递增。 例如，可使用以下命令下载模型并从本地文件注册它：

```azurecli-interactive
az ml model download -i "mymodel:1" -t .
az ml model register -n mymodel -p "sklearn_mnist_model.pkl"
```

第一个命令将注册的模型下载到当前目录。 文件名是 `sklearn_mnist_model.pkl`，这是注册模型时引用的文件。 第二个命令使用与前一个注册 (`mymodel`) 相同的名称注册本地模型 (`-p "sklearn_mnist_model.pkl"`)。 这次，返回的 JSON 数据将版本列为 2。

## <a name="deploy-the-model"></a>部署模型

若要部署模型，请使用以下命令：

```azurecli-interactive
az ml model deploy -n myservice -m "mymodel:1" --ic inferenceConfig.json --dc aciDeploymentConfig.yml
```

> [!NOTE]
> 你可能会收到“未能检查 LocalWebservice 是否存在”或“未能创建 Docker 客户端”警告。 可放心地忽略该警告，因为你无需部署本地 Web 服务。

此命令使用先前注册的模型的版本 1 部署一个名为 `myservice` 的新服务。

`inferenceConfig.yml` 文件提供了有关如何使用模型进行推理的信息。 例如，它引用入口脚本 (`score.py`) 和软件依赖项。

有关此文件结构的详细信息，请参阅[推理配置架构](reference-azure-machine-learning-cli.md#inference-configuration-schema)。 有关入口脚本的详细信息，请参阅[使用 Azure 机器学习部署模型](how-to-deploy-and-where.md#prepare-to-deploy)。

`aciDeploymentConfig.yml` 描述用于承载服务的部署环境。 部署配置特定于用于部署的计算类型。 在本例中，使用 Azure 容器实例。 有关详细信息，请参阅[部署配置架构](reference-azure-machine-learning-cli.md#deployment-configuration-schema)。

部署过程需要几分钟才能完成。

> [!TIP]
> 本示例使用 Azure 容器实例。 部署到 ACI 会自动创建所需的 ACI 资源。 如果转而部署到 Azure Kubernetes 服务，则必须提前创建 AKS 群集，并将其指定为 `az ml model deploy` 命令的一部分。 有关部署到 AKS 的示例，请参阅[将模型部署到 Azure Kubernetes 服务群集](how-to-deploy-azure-kubernetes-service.md)。

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

从部署返回的 `scoringUri` 是作为 Web 服务部署的模型的 REST 终结点。 还可使用以下命令获取此 URI：

```azurecli-interactive
az ml service show -n myservice
```

此命令会返回相同的 JSON 文档，包括 `scoringUri`。

REST 终结点可用于向服务发送数据。 要了解如何创建向服务发送数据的客户端应用程序，请参阅[使用部署为 Web 服务的 Azure 机器学习模型](how-to-consume-web-service.md)

### <a name="send-data-to-the-service"></a>向服务发送数据

虽然可以创建客户端应用程序来调用终结点，但机器学习 CLI 提供了一个可用作测试客户端的实用程序。 请使用以下命令将 `testdata.json` 文件中的数据发送到服务：

```azurecli-interactive
az ml service run -n myservice -d @testdata.json
```

> [!TIP]
> 如果使用 PowerShell，则改用以下命令：
>
> ```azurecli-interactive
> az ml service run -n myservice -d `@testdata.json
> ```

命令的响应类似于 `[ 3 ]`。

## <a name="clean-up-resources"></a>清理资源

> [!IMPORTANT]
> 已创建的资源可以用作其他 Azure 机器学习教程和操作方法文章的先决条件。

### <a name="delete-deployed-service"></a>删除已部署的服务

如果你打算继续使用 Azure 机器学习工作区，但想要删除已部署的服务来降低成本，请使用以下命令：

```azurecli-interactive
az ml service delete -n myservice
```

此命令会返回一个 JSON 文档，其中有已删除的服务的名称。 删除服务可能需要几分钟时间。

### <a name="delete-the-training-compute"></a>删除训练计算

如果你打算继续使用 Azure 机器学习工作区，但想要删除创建用于训练的 `cpu-cluster` 计算目标，请使用以下命令：

```azurecli-interactive
az ml computetarget delete -n cpu-cluster
```

此命令会返回一个 JSON 文档，其中有已删除的计算目标的 ID。 删除计算目标可能需要几分钟时间。

### <a name="delete-everything"></a>删除所有内容

如果不打算使用已创建的资源，请删除它们，以免产生额外的费用。

若要删除资源组以及在此文档中创建的所有 Azure 资源，请使用以下命令。 将 `<resource-group-name>` 替换为之前创建的资源组的名称：

```azurecli-interactive
az group delete -g <resource-group-name> -y
```

## <a name="next-steps"></a>后续步骤

在这篇 Azure 机器学习教程中，你使用机器学习 CLI 执行了以下任务：

> [!div class="checklist"]
> * 安装机器学习扩展
> * 创建 Azure 机器学习工作区
> * 创建用于训练模型的计算资源
> * 定义并注册用于训练模型的数据集
> * 开始训练运行
> * 注册和下载模型
> * 将模型部署为 Web 服务
> * 使用 Web 服务对数据评分

要详细了解如何使用 CLI，请参阅[将 CLI 扩展用于 Azure 机器学习](reference-azure-machine-learning-cli.md)。
