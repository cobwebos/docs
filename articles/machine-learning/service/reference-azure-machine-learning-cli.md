---
title: 机器学习 CLI 扩展
titleSuffix: Azure Machine Learning service
description: 了解适用于 Azure CLI 的 Azure 机器学习的机器学习 CLI 扩展。 Azure CLI 是一个跨平台命令行实用工具，可让你使用 Azure 云中的资源。 借助该机器学习扩展可以使用 Azure 机器学习服务。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: e16506773e38f1732a55161cdd58ffb7523602d4
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/12/2018
ms.locfileid: "53277278"
---
# <a name="use-the-cli-extension-for-azure-machine-learning-service"></a>将 CLI 扩展用于 Azure 机器学习服务

Azure 机器学习 CLI 是 [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)（适用于 Azure 平台的跨平台命令行接口）的一个扩展。 借助此扩展提供的命令，可以从命令行使用 Azure 机器学习服务。 它允许创建用于自动化机器学习工作流的脚本。 例如，可以创建用于执行以下操作的脚本：

+ 运行试验以创建机器学习模型

+ 注册机器学习模型以供客户使用

+ 打包、部署和跟踪机器学习模型的生命周期

CLI 不能取代 Azure 机器学习 SDK。 它是一个经过优化的补充工具，可以处理高度参数化的任务，例如：

* 创建计算资源

* 参数化试验提交

* 模型的注册

* 映像创建

* 服务部署

## <a name="prerequisites"></a>先决条件


* 若要使用 CLI，必须拥有 Azure 订阅。 如果还没有 Azure 订阅，请在开始前创建免费帐户。 立即试用 [Azure 机器学习服务免费版或付费版](http://aka.ms/AMLFree)。

* [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)。

## <a name="install-the-extension"></a>安装扩展

若要安装机器学习 CLI 扩展，请使用以下命令：

```azurecli-interactive
az extension add -s https://azuremlsdktestpypi.blob.core.windows.net/wheels/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1/azure_cli_ml-1.0.2-py2.py3-none-any.whl --pip-extra-index-urls  https://azuremlsdktestpypi.azureedge.net/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1
```

出现提示时，选择 `y` 安装该扩展。

若要验证是否已安装该扩展，请使用以下命令显示特定于机器学习的子命令列表：

```azurecli-interactive
az ml -h
```

> [!TIP]
> 若要更新该扩展，必须先将其__删除__，然后再__安装__。 这会安装最新版本。

## <a name="remove-the-extension"></a>删除扩展

若要删除 CLI 扩展，请使用以下命令：

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>资源管理

以下命令演示如何使用 CLI 来管理 Azure 机器学习所用的资源。


+ 创建 Azure 机器学习服务工作区：

    ```azurecli-interactive
    az ml workspace create -n myworkspace -g myresourcegroup
    ```

+ 设置默认工作区：

    ```azurecli-interactive
    az configure --defaults aml_workspace=myworkspace group=myresourcegroup
    ```

+ 为分布式定型创建托管的计算目标：

    ```azurecli-interactive
    az ml computetarget create amlcompute -n mycompute --max_nodes 4 --size Standard_NC6
    ```

* 更新托管的计算目标：

    ```azurecli-interactive
    az ml computetarget update --name mycompute --workspace –-group --max_nodes 4 --min_nodes 2 --idle_time 300
    ```

* 附加非托管计算目标以进行定型或部署：

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myrg -w myworkspace
    ```

## <a name="experiments"></a>试验

以下命令演示如何通过 CLI 使用试验：

* 提交试验之前附加项目（运行配置）：

    ```azurecli-interactive
    az ml project attach --experiment-name myhistory
    ```

* 开始运行试验。 使用此命令时，请指定计算目标。 在此示例中，`local` 使用本地计算机通过 `train.py` 脚本训练模型：

    ```azurecli-interactive
    az ml run submit -c local train.py
    ```

* 查看提交的试验列表：

    ```azurecli-interactive
    az ml history list
    ```

## <a name="model-registration-image-creation--deployment"></a>模型注册、映像创建和部署

以下命令演示如何注册已训练的模型，然后将其部署为生产服务：

+ 将模型注册到 Azure 机器学习：

  ```azurecli-interactive
  az ml model register -n mymodel -m sklearn_regression_model.pkl
  ```

+ 创建包含机器学习模型和依赖项的映像： 

  ```azurecli-interactive
  az ml image create container -n myimage -r python -m mymodel:1 -f score.py -c myenv.yml
  ```

+ 将映像部署到计算目标：

  ```azurecli-interactive
  az ml service create aci -n myaciservice --image-id myimage:1
  ```
