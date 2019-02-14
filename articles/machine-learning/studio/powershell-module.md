---
title: 适用于机器学习工作室的 PowerShell 模块
titleSuffix: Azure Machine Learning Studio
description: 使用 PowerShell 创建和管理 Azure 机器学习工作室工作区、实验和 Web 服务等。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: previous-ms.author=haining, previous-author=hning86
ms.date: 01/25/2019
ms.openlocfilehash: b6080e6e81b6cedce4d08cf20e082c87e5cecade
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2019
ms.locfileid: "55489812"
---
# <a name="powershell-modules-for-azure-machine-learning-studio"></a>Azure 机器学习工作室 PowerShell 模块

使用 PowerShell 模块，可采用编程方式管理工作室资源和资产，例如工作区、数据集和 Web 服务。

你可以使用以下三个 Powershell 模块与工作室资源进行交互：

* 于 2018 年发布的 [Azure PowerShell Az](#az-rm)，包含 AzureRM 的所有功能，尽管使用不同的 cmdlet 名称
* 于 2016 年发布的 [AzureRM](#az-rm)
* 于 2016 年发布的 [Azure 机器学习 PowerShell（经典）](#classic)

虽然这些模块有一些相似之处，但每个模块都是针对特定场景设计的。 本文介绍了 PowerShell 模块之间的差异，帮助你决定选择哪些模块。

## <a name="choosing-modules"></a> 选择模块

根据所管理的资源类型，在可用的 PowerShell 模块之间进行选择。

检查下面的[支持表](#support-table)，查看每个模块支持哪些资源。 由于 PowerShell（经典）可与 Az 或 AzureRM 并行安装，因此，可以安装两个模块并覆盖所有资源类型（经典和 Az 或经典和 AzureRM）

但是，不建议同时安装 Az 和 AzureRM。 若要在 Az 和 AzureRM 之间做出选择，Microsoft 建议在未来所有部署中使用 Az。 仅在环境中有特殊情况需要时才使用 AzureRm。

要了解更多关于 Az 和 AzureRM 之间的差异，以及我们提供的迁移路径，请参阅我们的 [Azure PowerShell Az 简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az)。

## <a name="az-rm"></a> Azure PowerShell Az 和 AzureRM

Az 和 AzureRM 都管理使用 Azure 资源管理器部署模型部署的解决方案。 这些资源包括工作室工作区和工作室新的 Web 服务。 若要使用经典部署模型管理部署的资源，应使用 PowerShell 经典模块。 若要了解有关部署模型的详细信息，请参阅 [Azure 资源管理器与经典部署](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model)一文。

Az 现在是与 Azure 交互的 PowerShell 模块，它包含 AzureRM 以前的所有功能。 AzureRM 将继续接收 bug 修复，但它不会接收任何新的 cmdlet 或功能。 虽然有一个从 AzureRM 升级的路径，但如果在使用工作室时遇到 Az 问题，请报告问题，然后重新使用 AzureRM。

若要开始使用 Az，请按照 [Azure Az 的安装说明](https://docs.microsoft.com/powershell/azure/install-az-ps)操作。

## <a name="classic"></a> PowerShell（经典）

通过工作室 [PowerShell 经典模块](https://aka.ms/amlps)，可让你管理使用经典部署模型部署的资源。 这些资源包括工作室用户资产、经典 Web 服务和经典 Web 服务终结点。

然而，Microsoft 建议为所有新资源使用资源管理器部署模型，以简化资源的部署和管理。 若要了解有关部署模型的详细信息，请参阅 [Azure 资源管理器与经典部署](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model)一文。

若要开始使用 PowerShell（经典），请从 GitHub 下载[发行包](https://github.com/hning86/azuremlps/releases)并按照[安装说明](https://github.com/hning86/azuremlps/blob/master/README.md)执行操作。 这些说明解释了如何取消阻止已下载/解压缩的 DLL，然后将它导入到 PowerShell 环境。

## <a name="support-table"></a> PowerShell 支持表

 **工作室工作区** | **Az** |  **AzureRM** | **PowerShell 经典** |
| --- | --- | --- | --- | --- |
| 创建/删除工作区 | [资源管理器模板](https://docs.microsoft.com/azure/machine-learning/studio/deploy-with-resource-manager-template) | [资源管理器模板](https://docs.microsoft.com/azure/machine-learning/studio/deploy-with-resource-manager-template) |  |
| 管理工作区用户 |  |  | [Add-AmlWorkspaceUsers](https://github.com/hning86/azuremlps#add-amlworkspaceusers)|
| 管理承诺计划 | [New-AzMlCommitmentPlan](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlcommitmentplan) | [New-AzureRmMlCommitmentPlan](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/new-azurermmlcommitmentplan) |
|||
| **Web 服务** | **Az** | **AzureRM** | **PowerShell 经典** |
| 管理 Web 服务 | [New-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlwebservice) <br> （新 Web 服务） | [New-AzureRmMlWebService](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/new-azurermmlwebservice) <br> （新 Web 服务） |[New-AmlWebService](https://github.com/hning86/azuremlps#manage-classic-web-service) <br> （经典 Web 服务） |
| 管理终结点/密钥 |  [Get-AzMlWebServiceKeys](https://docs.microsoft.com/powershell/module/az.machinelearning/get-azmlwebservicekeys) <br> （新 Web 服务） | [Get-AzureRmMlWebServiceKeys](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/get-azurermmlwebservicekeys) <br> （新 Web 服务） | [Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#manage-classic-web-servcie-endpoint) <br> （经典 Web 服务） |
|||
| **用户资产** | **Az** | **AzureRM** | **PowerShell 经典** |
| 管理数据集/定型模型 |  |  | [Get-AmlDataset](https://github.com/hning86/azuremlps#manage-user-assets-dataset-trained-model-transform) |
| 管理试验 |  |  | [Start-AmlExperiment](https://github.com/hning86/azuremlps#manage-experiment) |
| 管理自定义模块 |  |  | [New-AmlCustomModule](https://github.com/hning86/azuremlps#manage-custom-module) |


## <a name="next-steps"></a>后续步骤
通过下面的链接，可找到 PowerShell 模块的完整文档：
* [AzureRM](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/#machine_learning)
* [PowerShell 经典](https://aka.ms/amlps)
* [Azure PowerShell Az](https://docs.microsoft.com/powershell/module/az.machinelearning/#machine_learning)
