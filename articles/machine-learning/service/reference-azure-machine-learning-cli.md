---
title: 机器学习 CLI 扩展
titleSuffix: Azure Machine Learning service
description: 了解适用于 Azure CLI 的 Azure 机器学习的机器学习 CLI 扩展。 Azure CLI 是一个跨平台命令行实用工具，可让你使用 Azure 云中的资源。 借助该机器学习扩展可以使用 Azure 机器学习服务。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: be3cedc4b496f4f64a52217099f64092dfb49228
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2019
ms.locfileid: "65149851"
---
# <a name="use-the-cli-extension-for-azure-machine-learning-service"></a>将 CLI 扩展用于 Azure 机器学习服务

Azure 机器学习 CLI 是 [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)（适用于 Azure 平台的跨平台命令行接口）的一个扩展。 此扩展提供了有关使用 Azure 机器学习服务的命令。 它可以自动将机器学习活动。 以下列表提供了如何使用 CLI 扩展某些示例操作：

+ 运行试验以创建机器学习模型

+ 注册机器学习模型以供客户使用

+ 打包、部署和跟踪机器学习模型的生命周期

CLI 不能取代 Azure 机器学习 SDK。 它是经过优化，可处理满足本身也向自动化的高度参数化的任务的补充工具。

## <a name="prerequisites"></a>必备组件

* 若要使用 CLI，必须拥有 Azure 订阅。 如果还没有 Azure 订阅，请在开始前创建免费帐户。 立即试用 [Azure 机器学习服务免费版或付费版](https://aka.ms/AMLFree)。

* [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)。

## <a name="install-the-extension"></a>安装扩展

若要安装机器学习 CLI 扩展，请使用以下命令：

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!TIP]
> 可以找到示例文件，可以使用以下命令使用[此处](http://aka.ms/azml-deploy-cloud)。

出现提示时，选择 `y` 安装该扩展。

若要验证是否已安装该扩展，请使用以下命令显示特定于机器学习的子命令列表：

```azurecli-interactive
az ml -h
```

## <a name="remove-the-extension"></a>删除扩展

若要删除 CLI 扩展，请使用以下命令：

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>资源管理

以下命令演示如何使用 CLI 来管理 Azure 机器学习所用的资源。

+ 如果你还没有一个创建的资源组：

    ```azurecli-interactive
    az group create -n myresourcegroup -l westus2
    ```

+ 创建 Azure 机器学习服务工作区：

    ```azurecli-interactive
    az ml workspace create -w myworkspace -g myresourcegroup
    ```

    有关详细信息，请参阅[az ml 工作区中创建](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-create)。

+ 将工作区配置附加到文件夹，以启用 CLI 上下文感知。
    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    有关详细信息，请参阅[az ml 文件夹附加](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach)。

+ 作为一种数据存储附加的 Azure blob 容器。

    ```azurecli-interactive
    az ml datastore attach-blob  -n datastorename -a accountname -c containername
    ```

    有关详细信息，请参阅[az ml 数据存储附加的 blob](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest#ext-azure-cli-ml-az-ml-datastore-attach-blob)。

    
+ 附加的 AKS 群集作为计算目标。

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myresourcegroup -w myworkspace
    ```

    有关详细信息，请参阅[az ml computetarget 附加 aks](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks)

+ 创建新的 AMLcompute 目标。

    ```azurecli-interactive
    az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
    ```

    有关详细信息，请参阅[az ml computetarget 创建 amlcompute](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute)。

## <a id="experiments"></a>运行试验

* 开始运行试验。 使用此命令时，指定的 runconfig 文件的名称 (前的文本\*.runconfig 如果正在寻求通过文件系统) 与-c 参数。

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    有关详细信息，请参阅[az ml 运行提交脚本](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script)。

* 查看实验的列表：

    ```azurecli-interactive
    az ml experiment list
    ```

    有关详细信息，请参阅[az ml 试验列表](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list)。

## <a name="model-registration-profiling-deployment"></a>模型注册，分析，部署

以下命令演示如何注册已训练的模型，然后将其部署为生产服务：

+ 将模型注册到 Azure 机器学习：

    ```azurecli-interactive
    az ml model register -n mymodel -p sklearn_regression_model.pkl
    ```

    有关详细信息，请参阅[az ml 模型注册](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register)。

+ **可选**分析您的模型部署为获得最佳的 CPU 和内存值。
    ```azurecli-interactive
    az ml model profile -n myprofile -m mymodel:1 --ic inferenceconfig.json -d "{\"data\": [[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}" -t myprofileresult.json
    ```

    有关详细信息，请参阅[az ml 模型配置文件](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile)。

+ 将模型部署到 AKS

    ```azurecli-interactive
    az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
    ```

    有关详细信息，请参阅[az ml 模型部署](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy)。


## <a name="next-steps"></a>后续步骤

* [命令参考的机器学习 CLI 扩展](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest)。
