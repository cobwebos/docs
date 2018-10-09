---
title: 关于 Azure 机器学习 CLI 扩展
description: 了解 Azure 机器学习的机器学习 CLI 扩展。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: reference
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 09/24/2018
ms.openlocfilehash: f8dae6de835173181430a98c19c7dd1fb3ebaa9f
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2018
ms.locfileid: "47158897"
---
# <a name="what-is-the-azure-machine-learning-cli"></a>什么是 Azure 机器学习 CLI？

Azure 机器学习命令行界面 (CLI) 扩展适用于使用 Azure 机器学习服务的数据科学家和开发人员。 它允许你快速自动化机器学习工作流程并将其投入生产，例如：
+ 运行试验以创建机器学习模型

+ 注册机器学习模型以供客户使用

+ 打包、部署和跟踪机器学习模型的生命周期

此机器学习 CLI 是 [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) 的扩展，在基于 Python 的适用于 Azure 机器学习服务的 <a href="http://aka.ms/aml-sdk" target="_blank">SDK</a> 的基础上生成。

> [!NOTE]
> CLI 目前处于早期预览阶段，并将进行更新。

## <a name="installing-and-uninstalling"></a>安装和卸载

可以从预览 PyPi 索引使用此命令安装 CLI：
```AzureCLI
az extension add -s https://azuremlsdktestpypi.blob.core.windows.net/wheels/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1/azure_cli_ml-0.1.50-py2.py3-none-any.whl --pip-extra-index-urls  https://azuremlsdktestpypi.azureedge.net/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1
```

可以使用此命令来删除 CLI：
```AzureCLI
az extension remove -n azure-cli-ml
```

可以使用上面的**删除**和**添加**步骤来更新 CLI。

## <a name="using-the-cli-vs-the-sdk"></a>使用 CLI 与使用 SDK
CLI 更适合由 dev-ops 角色进行自动化，或作为持续集成和交付管道的一部分。 CLI 经过优化，可以处理不常见且高度参数化的任务。 

示例包括：
- 计算预配
- 参数化试验提交
- 模型注册、映像创建
- 服务部署

建议数据科学家使用 Azure ML SDK。

## <a name="common-machine-learning-cli-commands"></a>常见的机器学习 CLI 命令
> [!NOTE]
> 可以在[此处](https://github.com/Azure/MachineLearningNotebooks/tree/cli/cli)找到可用于成功执行以下命令的示例文件。

使用丰富的 `az ml` 命令集在任何命令行环境中交互服务，包括 Azure 门户 cloud shell。

下面是常用命令的示例：

### <a name="workspace-creation--compute-setup"></a>工作区创建和计算设置

+ 创建 Azure 机器学习工作区，这是机器学习的顶级资源。
   ```AzureCLI
   az ml workspace create -n myworkspace -g myresourcegroup
   ```

+ 默认情况下，将 CLI 设置为使用此工作区。
   ```AzureCLI
   az configure --defaults aml_workspace=myworkspace group=myresourcegroup
   ```

+ 创建 DSVM（数据科学 VM）。 还可以创建用于分布式训练的 BatchAI 群集或用于部署的 AKS 群集。
  ```AzureCLI
  az ml computetarget setup dsvm -n mydsvm
  ```

### <a name="experiment-submission"></a>试验提交
+ 附加到项目（运行配置）以提交试验。 这用于跟踪试验运行。
  ```AzureCLI
  az ml project attach --experiment-name myhistory
  ```

+ 在选择的计算目标上针对 Azure 机器学习服务提交试验。 此示例将针对本地计算环境执行。 请确保 conda 环境文件捕获 python 依赖项。

  ```AzureCLI
  az ml run submit -c local train.py
  ```

+ 查看提交的试验列表。
```AzureCLI
az ml history list
```

### <a name="model-registration-image-ceation--deployment"></a>模型注册、映像创建和部署

+ 使用 Azure 机器学习注册模型。
  ```AzureCLI
  az ml model register -n mymodel -m sklearn_regression_model.pkl
  ```

+ 创建一个映像以包含你的机器学习模型和依赖项。 
  ```AzureCLI
  az ml image create container -n myimage -r python -m mymodel:1 -f score.py -c myenv.yml
  ```

+ 将打包的模型部署到包括 ACI 和 AKS 在内的目标。
  ```AzureCLI
  az ml service create aci -n myaciservice --image-id myimage:1
  ```
    
## <a name="full-command-list"></a>完整命令列表
可以通过运行 ```az ml COMMANDNAME -h``` 找到 CLI 扩展（及其支持的参数）的完整命令列表。 
