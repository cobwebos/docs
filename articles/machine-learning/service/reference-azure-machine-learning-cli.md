---
title: 机器学习 CLI 扩展
titleSuffix: Azure Machine Learning
description: 了解适用于 Azure CLI 的 Azure 机器学习的机器学习 CLI 扩展。 Azure CLI 是一个跨平台命令行实用工具，可让你使用 Azure 云中的资源。 机器学习扩展使你能够使用 Azure 机器学习。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 10/22/2019
ms.custom: seodec18
ms.openlocfilehash: dcbe535bb92f7755980929956093b0f0121ef41b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73476454"
---
# <a name="use-the-cli-extension-for-azure-machine-learning"></a>使用 CLI 扩展进行 Azure 机器学习
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure 机器学习 CLI 是 [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)（适用于 Azure 平台的跨平台命令行接口）的一个扩展。 此扩展提供用于处理 Azure 机器学习的命令。 它允许您自动执行机器学习活动。 以下列表提供了一些可以通过 CLI 扩展执行的示例操作：

+ 运行试验以创建机器学习模型

+ 注册机器学习模型以供客户使用

+ 打包、部署和跟踪机器学习模型的生命周期

CLI 不能取代 Azure 机器学习 SDK。 它是一种补充工具，经过优化，可处理高度参数化的任务，这些任务非常适合自动化。

## <a name="prerequisites"></a>必备组件

* 若要使用 CLI，必须拥有 Azure 订阅。 如果还没有 Azure 订阅，请在开始前创建免费帐户。 立即试用[Azure 机器学习免费版或付费版](https://aka.ms/AMLFree)。

* [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)。

## <a name="full-reference-docs"></a>完整参考文档

查找[Azure CLI 的 azure cli ml 扩展的完整参考文档](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/?view=azure-cli-latest)。

## <a name="install-the-extension"></a>安装扩展

若要安装机器学习 CLI 扩展，请使用以下命令：

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!TIP]
> 可在[此处](https://aka.ms/azml-deploy-cloud)找到可用于以下命令的示例文件。

出现提示时，选择 `y` 安装该扩展。

若要验证是否已安装该扩展，请使用以下命令显示特定于机器学习的子命令列表：

```azurecli-interactive
az ml -h
```

## <a name="update-the-extension"></a>更新扩展

若要更新机器学习 CLI 扩展，请使用以下命令：

```azurecli-interactive
az extension update -n azure-cli-ml
```


## <a name="remove-the-extension"></a>删除扩展

若要删除 CLI 扩展，请使用以下命令：

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>资源管理

以下命令演示如何使用 CLI 来管理 Azure 机器学习所用的资源。

+ 如果还没有资源组，请创建一个资源组：

    ```azurecli-interactive
    az group create -n myresourcegroup -l westus2
    ```

+ 创建 Azure 机器学习工作区：

    ```azurecli-interactive
    az ml workspace create -w myworkspace -g myresourcegroup
    ```

    有关详细信息，请参阅[az ml workspace create](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-create)。

+ 将工作区配置附加到文件夹，以启用 CLI 上下文感知。

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    此命令创建一个 `.azureml` 子目录，其中包含 .runconfig 和 conda 环境文件示例。 它还包含用于与 Azure 机器学习工作区进行通信的 `config.json` 文件。

    有关详细信息，请参阅[az ml folder attach](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach)。

+ 将 Azure blob 容器附加为数据存储。

    ```azurecli-interactive
    az ml datastore attach-blob  -n datastorename -a accountname -c containername
    ```

    有关详细信息，请参阅[az ml 数据存储附加 blob](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest#ext-azure-cli-ml-az-ml-datastore-attach-blob)。

+ 将文件上传到数据存储。

    ```azurecli-interactive
    az ml datastore upload  -n datastorename -p sourcepath
    ```

    有关详细信息，请参阅[az ml 数据存储上传](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest#ext-azure-cli-ml-az-ml-datastore-upload)。

+ 将 AKS 群集附加为计算目标。

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myresourcegroup -w myworkspace
    ```

    有关详细信息，请参阅[az ml computetarget attach aks](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks)

+ 创建新的 AMLcompute 目标。

    ```azurecli-interactive
    az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
    ```

    有关详细信息，请参阅[az ml computetarget create amlcompute](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute)。

## <a id="experiments"></a>运行试验

* 开始运行试验。 使用此命令时，请指定 .runconfig 文件的名称 \*（如果您正在查看文件系统，则为 .runconfig）。

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > `az ml folder attach` 命令将创建一个 `.azureml` 子目录，其中包含两个 .runconfig 文件示例。 
    >
    > 如果你的 Python 脚本以编程方式创建运行配置对象，则可以使用[.runconfig （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py#save-path-none--name-none--separate-environment-yaml-false-)将其另存为 .runconfig 文件。
    >
    > 在此[JSON 文件](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json)中可以找到完整的 .runconfig 架构。

    有关详细信息，请参阅[az ml run 提交脚本](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script)。

* 查看实验列表：

    ```azurecli-interactive
    az ml experiment list
    ```

    有关详细信息，请参阅[az ml 实验列表](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list)。

## <a name="environment-management"></a>环境管理

以下命令演示了如何创建、注册和列出工作区 Azure 机器学习[环境](how-to-configure-environment.md)：

+ 为环境创建基架文件：

    ```azurecli-interactive
    az ml environment scaffold -n myenv -d myenvdirectory
    ```

    有关详细信息，请参阅[az ml 环境基架](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-scaffold)。

+ 注册环境：

    ```azurecli-interactive
    az ml environment register -d myenvdirectory
    ```

    有关详细信息，请参阅[az ml 环境 register](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-register)。

+ 列出注册环境：

    ```azurecli-interactive
    az ml environment list
    ```

    有关详细信息，请参阅[az ml 环境 list](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-list)。

+ 下载已注册的环境：

    ```azurecli-interactive
    az ml environment download -n myenv -d downloaddirectory
    ```

    有关详细信息，请参阅[az ml 环境下载](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-download)。

## <a name="ml-pipeline-management"></a>ML 管道管理

以下命令演示了如何使用机器学习管道：

+ 创建机器学习管道：

    ```azurecli-interactive
    az ml pipeline create -n mypipeline -y mypipeline.yml
    ```

    有关详细信息，请参阅[az ml 管道 create](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/pipeline?view=azure-cli-latest#ext-azure-cli-ml-az-ml-pipeline-create)。

    有关管道 YAML 文件的详细信息，请参阅[在 YAML 中定义机器学习管道](reference-pipeline-yaml.md)。

+ 运行管道：

    ```azurecli-interactive
    az ml run submit-pipeline -n myexperiment -y mypipeline.yml
    ```

    有关详细信息，请参阅[az ml run 提交管道](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-pipeline)。

    有关管道 YAML 文件的详细信息，请参阅[在 YAML 中定义机器学习管道](reference-pipeline-yaml.md)。

+ 计划管道：

    ```azurecli-interactive
    az ml pipeline create-schedule -n myschedule -e myexpereiment -i mypipelineid -y myschedule.yml
    ```

    有关详细信息，请参阅[az ml 漏斗 create-schedule](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/pipeline?view=azure-cli-latest#ext-azure-cli-ml-az-ml-pipeline-create-schedule)。

    有关管道计划 YAML 文件的详细信息，请参阅[在 YAML 中定义机器学习管道](reference-pipeline-yaml.md#schedules)。

## <a name="model-registration-profiling-deployment"></a>模型注册，分析，部署

以下命令演示如何注册已训练的模型，然后将其部署为生产服务：

+ 将模型注册到 Azure 机器学习：

    ```azurecli-interactive
    az ml model register -n mymodel -p sklearn_regression_model.pkl
    ```

    有关详细信息，请参阅[az ml model register](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register)。

+ **可选**分析模型，获取部署的最佳 CPU 和内存值。
    ```azurecli-interactive
    az ml model profile -n myprofile -m mymodel:1 --ic inferenceconfig.json -d "{\"data\": [[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}" -t myprofileresult.json
    ```

    有关详细信息，请参阅[az ml model profile](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile)。

+ 将模型部署到 AKS
    ```azurecli-interactive
    az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
    ```
    
    有关推理配置文件架构的详细信息，请参阅[推理配置架构](#inferenceconfig)。
    
    有关部署配置文件架构的详细信息，请参阅[部署配置架构](#deploymentconfig)。

    有关详细信息，请参阅[az ml model deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy)。

<a id="inferenceconfig"></a>

## <a name="inference-configuration-schema"></a>推理配置架构

[!INCLUDE [inferenceconfig](../../../includes/machine-learning-service-inference-config.md)]

<a id="deploymentconfig"></a>

## <a name="deployment-configuration-schema"></a>部署配置架构

### <a name="local-deployment-configuration-schema"></a>本地部署配置架构

[!INCLUDE [deploymentconfig](../../../includes/machine-learning-service-local-deploy-config.md)]

### <a name="azure-container-instance-deployment-configuration-schema"></a>Azure 容器实例部署配置架构 

[!INCLUDE [deploymentconfig](../../../includes/machine-learning-service-aci-deploy-config.md)]

### <a name="azure-kubernetes-service-deployment-configuration-schema"></a>Azure Kubernetes 服务部署配置架构

[!INCLUDE [deploymentconfig](../../../includes/machine-learning-service-aks-deploy-config.md)]

## <a name="next-steps"></a>后续步骤

* [机器学习 CLI 扩展的命令参考](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest)。

* [使用 Azure Pipelines 训练和部署机器学习模型](/azure/devops/pipelines/targets/azure-machine-learning)
