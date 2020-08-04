---
author: gvashishtha
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: include
ms.date: 07/31/2020
ms.author: gopalv
ms.openlocfilehash: 38b346b4296ea72f964717220adf762e8d0d6d91
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2020
ms.locfileid: "87542755"
---
## <a name="prerequisites"></a>先决条件

- Azure 机器学习工作区。 有关详细信息，请参阅[创建 Azure 机器学习工作区](../articles/machine-learning/how-to-manage-workspace.md)。
- 模型。 如果没有已训练的模型，则可以使用[此教程](https://aka.ms/azml-deploy-cloud)中提供的模型和依赖项文件。
- [机器学习服务的 Azure 命令行接口（CLI）扩展](../articles/machine-learning/reference-azure-machine-learning-cli.md)


## <a name="connect-to-your-workspace"></a>连接到工作区

按照 Azure CLI 文档中的说明[设置你的订阅上下文](/cli/azure/manage-azure-subscriptions-azure-cli#change-the-active-subscription)。

然后执行以下操作：

```azurecli-interactive
az ml workspace list --resource-group=<my resource group>
```

查看有权访问的工作区。

## <a name="register-your-model"></a><a id="registermodel"></a> 注册模型

已注册的模型是组成模型的一个或多个文件的逻辑容器。 例如，如果有一个存储在多个文件中的模型，则可以在工作区中将这些文件注册为单个模型。 注册这些文件后，可以下载或部署已注册的模型，并接收注册的所有文件。

> [!TIP]
> 注册模型时，请提供云位置（来自训练运行）或本地目录的路径。 此路径仅用于在注册过程中查找要上传的文件。 它不需要与入口脚本中使用的路径匹配。 有关详细信息，请参阅[在入口脚本中查找模型文件](../articles/machine-learning/how-to-deploy-advanced-entry-script.md#load-registered-models)。

机器学习模型会注册到 Azure 机器学习工作区中。 模型可以来自 Azure 机器学习或其他位置。 注册模型时，可以选择提供有关模型的元数据。 然后，可将应用于模型注册的 `tags` 和 `properties` 字典用于筛选模型。

以下示例演示如何注册模型。

### <a name="register-a-model-from-an-azure-ml-training-run"></a>从 Azure ML 定型运行注册模型

```azurecli-interactive
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid --tag area=mnist
```

[!INCLUDE [install extension](machine-learning-service-install-extension.md)]

`--asset-path` 参数表示模型的云位置。 本示例使用的是单个文件的路径。 若要在模型注册中包含多个文件，请将 `--asset-path` 设置为包含文件的文件夹的路径。

### <a name="register-a-model-from-a-local-file"></a>通过本地文件注册模型

```azurecli-interactive
az ml model register -n onnx_mnist -p mnist/model.onnx
```

若要在模型注册中包含多个文件，请将 `-p` 设置为包含文件的文件夹的路径。

有关的详细信息 `az ml model register` ，请参阅[参考文档](/cli/azure/ext/azure-cli-ml/ml/model)。

## <a name="define-an-entry-script"></a>定义条目脚本

[!INCLUDE [write entry script](machine-learning-entry-script.md)]

## <a name="define-an-inference-configuration"></a>定义推理配置

[!INCLUDE [inference config](machine-learning-service-inference-config.md)]

以下命令演示如何使用 CLI 部署模型：

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

此实例中的配置指定以下设置：

* 此模型需要 Python
* 用于处理发送到已部署服务的 web 请求的[条目脚本](#define-an-entry-script)
* 描述推理所需 Python 包的 Conda 文件

若要详细了解如何将自定义 Docker 映像与推理配置结合使用，请参阅[如何使用自定义 Docker 映像部署模型](../articles/machine-learning/how-to-deploy-custom-docker-image.md)。

## <a name="choose-a-compute-target"></a>选择计算目标

[!INCLUDE [aml-compute-target-deploy](aml-compute-target-deploy.md)]

## <a name="define-a-deployment-configuration"></a>定义部署配置

根据所选的计算目标，部署配置可用的选项会有所不同。

[!INCLUDE [aml-local-deploy-config](machine-learning-service-local-deploy-config.md)]

有关详细信息，请参阅 [az ml 模型部署](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-deploy)文档。

## <a name="deploy-your-model"></a>部署模型

你现在已准备好部署模型。

### <a name="using-a-registered-model"></a>使用已注册的模型

如果在 Azure 机器学习工作区中注册了模型，请将 "mymodel>： 1" 替换为模型的名称及其版本号。

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

### <a name="using-a-local-model"></a>使用本地模型

如果你不想注册模型，则可以将 inferenceconfig.js中的 "sourceDirectory" 参数传递给，以指定用于提供模型的本地目录。

```azurecli-interactive
az ml model deploy --ic inferenceconfig.json --dc deploymentconfig.json
```
## <a name="delete-resources"></a>删除资源

若要删除已部署的 webservice，请使用 `az ml service <name of webservice>` 。

若要从工作区中删除已注册的模型，请使用`az ml model delete <model id>`

阅读有关[删除 webservice](/cli/azure/ext/azure-cli-ml/ml/service#ext-azure-cli-ml-az-ml-service-delete)并[删除模型](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-delete)的详细信息