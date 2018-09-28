---
title: 迁移到 Azure 机器学习服务
description: 了解如何从早期版本升级或迁移到最新版本的 Azure 机器学习服务。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: haining
author: haining
ms.date: 09/24/2018
ms.openlocfilehash: 2182a39836f02596d22168722e6ece7a2872dccc
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46969472"
---
# <a name="migrate-to-the-latest-version-of-azure-machine-learning-service"></a>迁移到最新版本的 Azure 机器学习服务 

**如果已经安装 Workbench（预览版）应用程序和/或拥有试验和模型管理预览帐户，请使用本文迁移到最新版本。**  如果没有安装预览版 Workbench，或者没有试验和/或模型管理帐户，则无需进行任何迁移。

## <a name="what-can-i-migrate"></a>我可以迁移什么？
在 Azure 机器学习服务的第一个预览中创建的大多数项目都存储在你自己的本地存储或云存储中。 这些项目不会消失。 要进行迁移，请使用更新的 Azure 机器学习服务再次注册这些项目。 

下表和文章说明了在迁移到最新版本的 Azure 机器学习服务之前或之后可以对现有资产和资源执行的操作。 在一段时间内，你还可以继续使用以前的版本和资产（[请参阅转换支持时间线](overview-what-happened-to-workbench.md#timeline)）。

|旧版本的资产或资源|是否可以迁移？|操作|
|-----------------|:-------------:|-------------|
|机器学习模型（作为本地文件）|是|无。 像以前一样工作。|
|模型依赖关系和架构（作为本地文件）|是|无。 像以前一样工作。|
|项目|是|[将本地文件夹附加到新工作区](#projects)。|
|运行历史记录|否|在一段时间内，[可下载](#history)。|
|数据准备文件|否|[准备任意大小的数据集](#dataprep)，以便使用新的 Azure 机器学习数据准备 SDK 进行建模或使用 Azure Databricks。|
|计算目标|否|在新工作区中注册它们。|
|已注册的模型|否|在新工作区下重新注册模型。|
|已注册的清单|否|无。 清单不再作为新工作区中的概念存在。|
|已注册的映像|否|在新工作区下重新创建部署 Docker 映像。|
|已部署的 Web 服务|否|无。 它们仍然会按原样工作 <br/>或[使用最新版本重新部署它们](#services)。|
|试验和 <br/>模型管理帐户|否|[改为创建工作区](#resources)。|
|机器学习 CLI 和 SDK|否|使用新的 [CLI](reference-azure-machine-learning-cli.md) 和 [SDK](http://aka.ms/aml-sdk) 执行新工作。|


详细了解[此版本中发生了哪些更改](overview-what-happened-to-workbench.md)？

>[!Warning]
>本文不适用于 Azure 机器学习工作室用户。 适用于已安装 Workbench（预览版）应用程序和/或拥有试验和模型管理预览帐户的 Azure 机器学习服务客户。

<a name="resources"></a>

## <a name="azure-resources"></a>Azure 资源

试验帐户、模型管理帐户以及机器学习计算环境等资源无法迁移到最新版本的 Azure 机器学习服务。 请参阅[时间线](overview-what-happened-to-workbench.md#timeline)，了解资产可以继续工作多长时间。

通过在 [Azure 门户](quickstart-get-started.md)中创建 Azure 机器学习工作区，开始使用最新版本。 门户的工作区仪表板仅在 Edge、Chrome 和 Firefox 浏览器上受支持。

此新工作区是顶级服务资源，使你可以使用 Azure 机器学习服务的所有最新功能。 [详细了解此工作区和体系结构](concept-azure-machine-learning-architecture.md)。

<a name="projects"></a>

## <a name="projects"></a>项目

项目现在是最新版本中本地计算机上的目录，而不是将项目放在云中的工作区中。 [请参阅最新体系结构的关系图](concept-azure-machine-learning-architecture.md)。 

要继续使用包含文件和脚本的本地目录，请在[“experiment.submit”](http://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py)Python 命令指定该目录的名称或使用“az ml project attach”CLI 命令。

例如：
```python
run = exp.submit(source_directory = script_folder, script = 'train.py', run_config = run_config_system_managed)
```

<a name="services"></a>

## <a name="deployed-web-services"></a>已部署的 Web 服务

若要迁移 Web 服务，请使用新 SDK 或 CLI 将模型重新部署到新的部署目标。 无需更改原始评分文件、模型文件依赖项文件、环境文件和架构文件。 

在最新版本中，模型作为 Web 服务部署到 [Azure 容器实例](how-to-deploy-to-aci.md) (ACI) 或 [Azure Kubernetes 服务](how-to-deploy-to-aks.md) (AKS) 群集。 

通过以下文章，了解详细信息：
+ [部署到 ACI](how-to-deploy-to-aci.md)
+ [部署到 AKS](how-to-deploy-to-aks.md)
+ [教程：使用 Azure 机器学习服务部署模型](tutorial-deploy-models-with-aml.md)

[对以前 CLI 的支持结束](overview-what-happened-to-workbench.md#timeline)后，你将无法管理最初使用模型管理帐户部署的 Web 服务。 但是，只要仍支持 Azure 容器服务 (ACS)，这些 Web 服务就将继续有效。

<a name="history"></a>

## <a name="run-history-records"></a>运行历史记录

虽然无法继续添加到旧工作区下的现有运行历史记录，但可以使用以前的 CLI 导出历史记录。 [对以前 CLI 的支持结束](overview-what-happened-to-workbench.md#timeline)后，将无法再导出这些运行历史记录。

使用新的 CLI 和 SDK 开始训练模型并跟踪运行历史记录。 可以了解如何使用[教程：使用 Azure 机器学习服务训练模型](tutorial-train-models-with-aml.md)。

要使用以前的 CLI 导出运行历史记录：

```azurecli
#list all runs
az ml history list

#get details about a particular run
az ml history info

# download all artifacts of a run
az ml history download
```

<a name="dataprep"></a>

## <a name="data-preparation-files"></a>数据准备文件
没有 Workbench，数据准备文件不可移植。 但你仍可以使用新的 Azure 机器学习数据准备 SDK 准备任意大小的数据集进行建模，或者使用 Azure Databricks 来处理大数据集。  [了解如何获取数据准备 SDK](how-to-data-prep.md)。 

## <a name="next-steps"></a>后续步骤

有关说明如何创建工作区、创建项目、运行脚本以及使用最新版本的 Azure 机器学习服务探索脚本的运行历史记录的快速入门，请尝试 [Azure 机器学习服务入门](quickstart-get-started.md)。

有关此工作流的更深入体验，请参阅完整教程，其中包含使用 Azure 机器学习服务训练和部署模型的详细步骤。 

> [!div class="nextstepaction"]
> [教程：训练和部署模型](tutorial-train-models-with-aml.md)
