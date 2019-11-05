---
title: 从 CLI 训练和部署模型
titleSuffix: Azure Machine Learning
description: 了解如何使用机器学习扩展进行 Azure CLI 从命令行定型、注册和部署模型。
ms.author: larryfr
author: Blackmist
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 09/12/2019
ms.openlocfilehash: 3f619caf7e2713e1c9251550b06c8bdefba5936f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493398"
---
# <a name="tutorial-train-and-deploy-a-model-from-the-cli"></a>教程：从 CLI 训练和部署模型
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本教程中，您将使用机器学习扩展来对 Azure CLI 进行定型、注册和部署模型。

本教程中的 Python 培训脚本使用[scikit-learn-了解](https://scikit-learn.org/)如何训练基本模型。 本教程的重点不在脚本或模型上，而是使用 CLI 处理 Azure 机器学习的过程。

了解如何执行以下操作：

> [!div class="checklist"]
> * 安装机器学习扩展
> * 创建 Azure 机器学习工作区
> * 创建用于定型模型的计算资源
> * 开始训练运行
> * 注册并下载模型
> * 将模型部署为 Web 服务
> * 使用 web 服务对数据进行评分

## <a name="prerequisites"></a>必备组件

* Azure 订阅。 如果还没有 Azure 订阅，请在开始前创建免费帐户。 立即试用[Azure 机器学习免费版或付费版](https://aka.ms/AMLFree)。

* 若要在**本地环境**中使用本文档中的 CLI 命令，需要[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

    如果使用[Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/)，则可以通过浏览器访问 CLI，并将其驻留在云中。

## <a name="download-the-example-project"></a>下载示例项目

对于本教程，请下载[https://github.com/microsoft/MLOps](https://github.com/microsoft/MLOps)项目。 本教程中的步骤使用 `model-training` 和 `model-deployment` 目录中的文件。

若要获取这些文件的本地副本，请[下载 .zip 存档](https://github.com/microsoft/MLOps/archive/master.zip)，或者使用以下 Git 命令克隆该存储库：

```azurecli-interactive
git clone https://github.com/microsoft/MLOps.git
```

### <a name="training-files"></a>训练文件

`model-training` 目录包含以下文件，这些文件在训练模型时使用：

* `.azureml\sklearn.runconfig`：__运行配置__文件。 此文件定义训练模型所需的运行时环境。
* `train-sklearn.py`：训练脚本。 此文件训练模型。
* `mylib.py`：由 `train-sklearn.py`使用的帮助器模块。
* `training-env.yml`：定义运行训练脚本所需的软件依赖关系。

训练脚本使用 scikit-learn 提供的糖尿病数据集来训练模型。

### <a name="deployment-files"></a>部署文件

`model-deployment` 目录包含以下文件，这些文件用于将训练的模型部署为 web 服务：

* `aciDeploymentConfig.yml`：__部署配置__文件。 此文件定义模型所需的宿主环境。
* `inferenceConfig.yml`：推理 configuration__ 文件。 此文件定义了服务用来对模型中的数据进行评分的软件环境。
* `score.py`：用于接受传入数据的 python 脚本，使用该模型对其进行评分，然后返回响应。
* `scoring-env.yml`：运行模型和 `score.py` 脚本所需的 conda 依赖项。

## <a name="connect-to-your-azure-subscription"></a>连接到 Azure 订阅

可以通过多种方法从 CLI 对 Azure 订阅进行身份验证。 最基本的使用浏览器进行交互身份验证。 若要以交互方式进行身份验证，请打开命令行或终端并使用以下命令：

```azurecli-interactive
az login
```

如果 CLI 可以打开默认的浏览器，则它会打开该浏览器并加载登录页。 否则，你需要打开浏览器并按照命令行中的说明进行操作。 说明涉及浏览到[https://aka.ms/devicelogin](https://aka.ms/devicelogin)并输入授权代码。

## <a name="install-the-machine-learning-extension"></a>安装机器学习扩展

若要安装机器学习扩展，请使用以下命令：

```azurecli-interactive
az extension add -n azure-cli-ml
```

如果你收到一条消息，指出已安装了该扩展，请使用以下命令更新到最新版本：

```azurecli-interactive
az extension update -n azure-cli-ml
```

## <a name="create-a-resource-group"></a>创建资源组

资源组是 Azure 平台上资源的基本容器。 使用 Azure 机器学习时，资源组将包含 Azure 机器学习工作区。 它还将包含工作区使用的其他 Azure 服务。 例如，如果你使用基于云的计算资源来训练模型，则会在资源组中创建该资源。

若要__创建新的资源组__，请使用以下命令。 将 `<resource-group-name>` 替换为此资源组要使用的名称。 将 `<location>` 替换为要用于此资源组的 Azure 区域：

> [!TIP]
> 应选择 Azure 机器学习可用的区域。 有关信息，请参阅[可用产品（按区域](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service)）。

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

有关使用资源组的详细信息，请参阅[az group](https://docs.microsoft.com//cli/azure/group?view=azure-cli-latest)。

## <a name="create-a-workspace"></a>创建工作区

若要创建新的工作区，请使用以下命令。 将 `<workspace-name>` 替换为要用于此工作区的名称。 将 `<resource-group-name>` 替换为资源组的名称：

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

在终端或命令提示符处，使用以下命令将目录更改为 `mlops` 目录，然后连接到工作区：

```azurecli-interactive
cd ~/mlops
az ml folder attach -w <workspace-name> -g <resource-group-name>
```

此命令的输出类似于以下 JSON：

```json
{
  "Experiment name": "model-training",
  "Project path": "/Code/MLOps/model-training",
  "Resource group": "<resource-group-name>",
  "Subscription id": "<subscription-id>",
  "Workspace name": "<workspace-name>"
}
```

此命令创建一个 `.azureml/config.json` 文件，该文件包含连接到你的工作区所需的信息。 本教程中使用的 `az ml` 命令的其余部分将使用此文件，因此你无需向所有命令添加工作区和资源组。

## <a name="create-the-compute-target-for-training"></a>创建用于定型的计算目标

此示例使用 Azure 机器学习计算群集来定型模型。 若要创建新的计算群集，请使用以下命令：

```azurecli-interactive
az ml computetarget create amlcompute -n cpu --max-nodes 4 --vm-size Standard_D2_V2
```

此命令的输出类似于以下 JSON：

```json
{
  "location": "<location>",
  "name": "cpu",
  "provisioningErrors": null,
  "provisioningState": "Succeeded"
}
```

此命令创建名为 `cpu`的新计算目标，最多包含四个节点。 所选的 VM 大小为 VM 提供 GPU 资源。 有关 VM 大小的信息，请参阅 [VM 类型和大小]。

> [!IMPORTANT]
> 计算目标的名称（在本例中为`cpu`）非常重要;它由下一部分中使用的 `.azureml/sklearn.runconfig` 文件引用。

## <a name="submit-the-training-run"></a>提交定型运行

若要开始在 `cpu` 计算目标上运行训练，请将目录更改为 `model-training` 目录，然后使用以下命令：

```azurecli-interactive
cd ~/mlops/model-training
az ml run submit-script -e myexperiment -c sklearn -d training-env.yml -t runoutput.json train-sklearn.py
```

此命令指定试验的名称（`myexperiment`）。 试验将有关此运行的信息存储在工作区中。

`-c sklearn` 参数指定 `.azureml/sklearn.runconfig` 文件。 如前文所述，此文件包含用于配置定型运行所使用的环境的信息。 如果检查此文件，你会看到它引用了之前创建的 `cpu` 计算目标。 它还列出了定型（`"nodeCount": "4"`）时要使用的节点数，并包含一个 `"condaDependenciees"` 部分，其中列出了运行训练脚本所需的 Python 包。

有关运行配置文件的详细信息，请参阅[设置并使用模型定型的计算目标](how-to-set-up-training-targets.md#create-run-configuration-and-submit-run-using-azure-machine-learning-cli)，或引用此[JSON 文件](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json)以查看 .runconfig 的完整架构。

`-t` 参数将对此运行的引用存储在 JSON 文件中，并将在后续步骤中用于注册和下载模型。

在训练过程中，它会在远程计算资源上流式传输来自定型会话的信息。 部分信息类似于以下文本：

```text
alpha is 0.80, and mse is 3340.02
alpha is 0.85, and mse is 3349.36
alpha is 0.90, and mse is 3359.09
alpha is 0.95, and mse is 3369.13


The experiment completed successfully. Finalizing run...
Cleaning up all outstanding Run operations, waiting 300.0 seconds
```

此文本从定型脚本（`train-sklearn.py`）记录，并显示此模型的两个性能度量值。 在这种情况下，我们希望模型的值最高。 性能指标特定于您所定型的模型。 其他模型将具有不同的性能指标。

如果检查 `train-sklearn.py`，您会注意到，在将训练的模型存储到文件时，它也会使用 alpha 值。 在这种情况下，它训练多个模型。 最高 alpha 应为最大的一个。 查看上面的输出和代码，将 alpha 为0.95 的模型另存为 `./outputs/ridge_0.95.pkl`

模型已保存到定型目标计算目标上的 `./outputs` 目录中。 在这种情况下，Azure 云中 Azure 机器学习计算实例。 训练过程会自动将 `./outputs` 目录的内容从发生定型的计算目标上载到 Azure 机器学习工作区。 它存储为试验的一部分（在本示例中为`myexperiment`）。

## <a name="register-the-model"></a>注册模型

若要在试验中直接从存储的版本注册模型，请使用以下命令：

```azurecli-interactive
az ml model register -n mymodel -f runoutput.json --asset-path "outputs/ridge_0.95.pkl" -t registeredmodel.json
```

此命令将定型运行创建的 `outputs/ridge_0.95.pkl` 文件注册为名为 `mymodel`的新模型注册。 `--assets-path` 引用试验中的路径。 在这种情况下，会从训练命令创建的 `runoutput.json` 文件中加载实验和运行信息。 `-t registeredmodel.json` 创建一个 JSON 文件，该文件引用此命令创建的新的已注册模型，并由适用于已注册模型的其他 CLI 命令使用。

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

记下为模型返回的版本号。 每次用此名称注册新模型时，版本都会递增。 例如，可以通过使用以下命令，下载模型并从本地文件注册它：

```azurecli-interactive
az ml model download -i "mymodel:1" -t .
az ml model register -n mymodel -p "ridge_0.95.pkl"
```

第一个命令将注册的模型下载到当前目录中。 文件名为 `ridge_0.95.pkl`，这是注册模型时引用的文件。 第二个命令将本地模型（`-p "ridge_0.95.pkl"`）注册为与先前注册（`mymodel`）相同的名称。 这一次，返回的 JSON 数据列出了版本2。

## <a name="deploy-the-model"></a>部署模型

若要部署模型，请将目录更改为 `model-deployment` 目录，然后使用以下命令：

```azurecli-interactive
cd ~/mlops/model-deployment
az ml model deploy -n myservice -m "mymodel:1" --ic inferenceConfig.yml --dc aciDeploymentConfig.yml
```

你可能会收到消息 "无法创建 Docker 客户端"。 您可以忽略此消息。 CLI 可以将 web 服务部署到本地 Docker 容器并检查 Docker。 在这种情况下，我们未使用本地部署。

此命令使用之前注册的模型版本1部署名为 `myservice`的新服务。

`inferenceConfig.yml` 文件提供了有关如何执行推断的信息，例如条目脚本（`score.py`）和软件依赖关系。 有关此文件结构的详细信息，请参阅[推理配置架构](reference-azure-machine-learning-cli.md#inference-configuration-schema)。 有关输入脚本的详细信息，请参阅[部署具有 Azure 机器学习的模型](how-to-deploy-and-where.md#prepare-to-deploy)。

`aciDeploymentConfig.yml` 描述用于承载服务的部署环境。 部署配置特定于用于部署的计算类型。 在这种情况下，将使用 Azure 容器实例。 有关详细信息，请参阅[部署配置架构](reference-azure-machine-learning-cli.md#deployment-configuration-schema)。

部署过程完成之前需要几分钟时间。

> [!TIP]
> 在此示例中，使用 Azure 容器实例。 部署到 ACI 会自动创建所需的 ACI 资源。 如果要改为部署到 Azure Kubernetes 服务，必须提前创建 AKS 群集并将其指定为 `az ml model deploy` 命令的一部分。 有关部署到 AKS 的示例，请参阅将[模型部署到 Azure Kubernetes Service 群集](how-to-deploy-azure-kubernetes-service.md)。

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

部署返回的 `scoringUri` 是部署为 web 服务的模型的 REST 终结点。 还可以使用以下命令获取此 URI：

```azurecli-interactive
az ml service show -n myservice
```

此命令返回同一个 JSON 文档，其中包括 `scoringUri`。

REST 终结点可用于向服务发送数据。 有关创建将数据发送到服务的客户端应用程序的信息，请参阅[使用部署为 web 服务的 Azure 机器学习模型](how-to-consume-web-service.md)

### <a name="send-data-to-the-service"></a>向服务发送数据

虽然您可以创建一个客户端应用程序来调用终结点，但机器学习 CLI 提供了一个可充当测试客户端的实用程序。 使用以下命令将测试数据发送到服务：

```azurecli-interactive
az ml service run -n myservice -d '{"data":[[1,2,3,4,5,6,7,8,9,10]]}'
```

命令的响应类似于 `[4684.920839774082]`。

## <a name="clean-up-resources"></a>清理资源

> [!IMPORTANT]
> 已创建的资源可以用作其他 Azure 机器学习教程和操作方法文章的先决条件。

### <a name="delete-deployed-service"></a>删除部署的服务

如果你计划继续使用 Azure 机器学习工作区，但想要消除已部署的服务以降低成本，请使用以下命令：

```azurecli-interactive
az ml service delete -n myservice
```

此命令将返回一个 JSON 文档，其中包含已删除服务的名称。 删除服务可能需要几分钟时间。

### <a name="delete-the-training-compute"></a>删除定型计算

如果计划继续使用 "Azure 机器学习" 工作区，但想要删除为定型创建的 `cpu` 计算目标，请使用以下命令：

```azurecli-interactive
az ml computetarget delete -n cpu
```

此命令将返回一个 JSON 文档，其中包含已删除计算目标的 ID。 删除计算目标可能需要几分钟时间。

### <a name="delete-everything"></a>删除所有内容

如果不打算使用创建的资源，请将其删除，以免产生额外的费用。

若要删除资源组以及在本文档中创建的所有 Azure 资源，请使用以下命令。 将 `<resource-group-name>` 替换为之前创建的资源组的名称：

```azurecli-interactive
az group delete -g <resource-group-name> -y
```

## <a name="next-steps"></a>后续步骤

在本 Azure 机器学习教程中，你使用了机器学习 CLI 来执行以下任务：

> [!div class="checklist"]
> * 安装机器学习扩展
> * 创建 Azure 机器学习工作区
> * 创建用于定型模型的计算资源
> * 开始训练运行
> * 注册并下载模型
> * 将模型部署为 Web 服务
> * 使用 web 服务对数据进行评分

有关使用 CLI 的详细信息，请参阅[将 cli 扩展用于 Azure 机器学习](reference-azure-machine-learning-cli.md)。
